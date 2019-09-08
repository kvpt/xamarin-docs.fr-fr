---
title: Comment effectuer une désinstallation complète de Xamarin pour Visual Studio ?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: c1742239-05ea-449d-9c99-611e5e5a90e4
author: conceptdev
ms.author: crdun
ms.date: 12/02/2016
ms.openlocfilehash: 1596e7ed7b3f6d71e13f19a64d111873efb7445c
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70764945"
---
# <a name="how-do-i-perform-a-thorough-uninstall-for-xamarin-for-visual-studio"></a>Comment effectuer une désinstallation complète de Xamarin pour Visual Studio ?

1. Dans le panneau de configuration Windows, désinstallez l’un des éléments suivants :

    - Xamarin
    - Xamarin pour Windows
    - Xamarin.Android
    - Xamarin.iOS
    - Xamarin pour Visual Studio

2. Dans l’Explorateur, supprimez tous les fichiers restants dans les dossiers d’extension Xamarin Visual Studio (toutes les versions, y compris les fichiers _programme_ et les _fichiers programme (x86)_ ) :

    _C :\\Program Files\*\\Microsoft Visual Studio\\1\*.0\\:extensions\\IDECommon7\\Xamarin_

3. Supprimez également le répertoire de cache des composants MEF de Visual Studio :

    _%LOCALAPPDATA%\\Microsoft\\VisualStudio\\1\*.0\\ComponentModelCache_

    En fait, cette étape est souvent suffisante pour résoudre les erreurs telles que :

    - « Le package «XamarinShellPackage » n’a pas été chargé correctement»

    - «Le fichier projet... ne peut pas être ouvert. Il manque un sous-type de projet»

    - «La référence d’objet n’est pas définie sur une instance d’un objet.  at Xamarin.VisualStudio.IOS.XamarinIOSPackage.Initialize()"

    - « Échec de la des éléments pour le package » (dans _ActivityLog. xml_de Visual Studio)

    - « LegacySitePackage a échoué pour le package » (dans _ActivityLog. xml_de Visual Studio)

    (Voir aussi l’extension Visual Studio [du cache du composant MEF Clear](https://visualstudiogallery.msdn.microsoft.com/22b94661-70c7-4a93-9ca3-8b6dd45f47cd) .  Et consultez le [bogue 40781, commentaire 19](https://bugzilla.xamarin.com/show_bug.cgi?id=40781#c19) pour un peu plus de contexte sur le problème en amont dans Visual Studio qui peut provoquer ces erreurs.)

4. Archivez également le répertoire _VirtualStore_ pour voir si Windows a stocké des fichiers de superposition pour les répertoires Xamarin ou _ComponentModelCache_ des _extensions\\_ :

    _% LocalAppData%\\VirtualStore_

5. Ouvrez l’éditeur du Registre`regedit`().

6. Recherchez la clé suivante :

    _HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Wow6432Node\\Microsoft\\Windows\\CurrentVersion\\SharedDlls_

7. Recherchez et supprimez toutes les entrées qui correspondent à ce modèle :

    _C :\\Program Files\*\\Microsoft Visual Studio\\1\*.0\\:extensions\\IDECommon7\\Xamarin_

8. Recherchez cette clé :

    _HKEY\_CURRENT\_USER\\Software\\Microsoft\\VisualStudio\\1\*.0\\ExtensionManager\\PendingDeletions_

9. Supprimez les entrées qui semblent éventuellement liées à Xamarin.  Par exemple, voici un qui était utilisé pour provoquer des problèmes dans les versions antérieures de Xamarin :

    _Mono.VisualStudio.Shell,1.0_

10. Ouvrez une invite `cmd.exe` de commandes d’administrateur, puis exécutez `devenv /setup` les `devenv /updateconfiguration` commandes et pour chaque version installée de Visual Studio.  Par exemple, pour Visual Studio 2015 :

    ```
    "%ProgramFiles(x86)%\Microsoft Visual Studio 14.0\Common7\IDE\devenv.exe" /setup
    "%ProgramFiles(x86)%\Microsoft Visual Studio 14.0\Common7\IDE\devenv.exe" /updateconfiguration
    ```

11. Redémarrez.

12. Réinstallez la version stable actuelle de Xamarin à l’aide de à partir de [VisualStudio.com](https://visualstudio.com/xamarin/).

## <a name="additional-troubleshooting-steps-for-package-did-not-load-correctly"></a>Étapes de dépannage supplémentaires pour « le package n’a pas été chargé correctement »

Dans les cas où les étapes ci-dessus ne résolvent pas l’erreur « le package n’a pas été correctement chargé », voici quelques étapes supplémentaires à essayer.

1. Créez un nouveau compte d’utilisateur Windows.

2. Vérifiez si les extensions Visual Studio Xamarin sont chargées sans erreur pour le nouvel utilisateur.

3. Si les extensions sont chargées correctement, le problème est probablement dû à certains des paramètres stockés pour l’utilisateur d’origine :

    - **Dans l’Explorateur** – _% LocalAppData\\%\\Microsoft\\VisualStudio\*1.0_
    - **Dans Regedit** – _HKEY\_Current\_user\\SoftwareMicrosoftVisualStudio\\1.\*0\\\\_
    - **Dans Regedit** – _HKEY\_Current\_user\\SoftwareMicrosoftVisualStudio\\10\*config\\\\\__

4. Si ces paramètres stockés semblent être le problème, vous pouvez essayer de les sauvegarder, puis de les supprimer.
