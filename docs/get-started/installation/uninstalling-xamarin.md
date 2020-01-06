---
title: Désinstaller Xamarin
description: Ce document décrit comment désinstaller Xamarin sur Mac et Windows. Il fournit des instructions spécifiques sur la désinstallation de Mono, Xamarin.Android, Xamarin.iOS et d’autres outils.
ms.prod: xamarin
ms.assetid: b83a85ec-842a-444c-8f82-c2464eda099b
author: conceptdev
ms.author: crdun
ms.date: 04/08/2017
ms.openlocfilehash: fa7d5fc8d33cd7ed3a347209aaff2610e6d319dc
ms.sourcegitcommit: 5ddb107b0a56bef8a16fce5bc6846f9673b3b22e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/31/2019
ms.locfileid: "75558818"
---
# <a name="uninstall-xamarin"></a>Désinstaller Xamarin

Ce guide explique comment supprimer Xamarin de macOS ou de Visual Studio sur Windows.

Si vous devez réinstaller Xamarin en utilisant Universal Installer, il est recommandé de toujours redémarrer l’ordinateur avant.

## <a name="uninstalling-xamarin-on-macos"></a>Désinstallation de Xamarin sur macOS

Ce guide peut être utilisé pour désinstaller chaque produit individuellement en accédant à la section appropriée. La totalité des outils Xamarin, qui comprennent les produits listés, peuvent être désinstallés en suivant ce guide jusqu’au bout :

- [Mono](#uninstallmono)
- [Xamarin.Android](#uninstallandroid)
- [Xamarin.iOS](#uninstallios)
- [Xamarin.Mac](#uninstallmac)
- [Workbooks](#uninstallworkbooks)
- [Xamarin Profiler](#uninstallprofiler)
- [Installer](#uninstallinstaller)

> [!TIP]
> Nous vous fournissons un [script de désinstallation](https://raw.githubusercontent.com/MicrosoftDocs/visualstudio-docs/master/mac/resources/uninstall-vsmac.sh) à utiliser lors de la suppression de Xamarin de votre machine macOS. Pour plus d’informations sur l’utilisation du script, consultez la section [Utilisation du script de désinstallation](#uninstallscript) de ce guide.

### <a name="uninstalling-visual-studio-for-mac"></a>Désinstallation de Visual Studio pour Mac

La première étape de désinstallation de Xamarin sur un Mac consiste à localiser **Visual Studio.app** dans le répertoire **/Applications** et à le faire glisser dans la **Corbeille**. Vous pouvez également cliquer avec le bouton droit et sélectionner **Placer dans la Corbeille**, comme illustré dans l’image suivante :

![Placer l’application Visual Studio dans la corbeille](uninstalling-xamarin-images/uninstall-image1.png)

La suppression de ce bundle d’applications supprime Visual Studio pour Mac, même s’il peut encore exister d’autres fichiers relatifs à Xamarin sur un système de fichiers.

Pour supprimer toutes les traces de Visual Studio pour Mac, exécutez les commandes suivantes dans Terminal :

```bash
sudo rm -rf "/Applications/Visual Studio.app"
rm -rf ~/Library/Caches/VisualStudio
rm -rf ~/Library/Preferences/VisualStudio
rm -rf ~/Library/Preferences/Visual\ Studio
rm -rf ~/Library/Logs/VisualStudio
rm -rf ~/Library/VisualStudio
rm -rf ~/Library/Preferences/Xamarin/
rm -rf ~/Library/Developer/Xamarin
rm -rf ~/Library/Application\ Support/VisualStudio
rm -rf ~/Library/Application\ Support/VisualStudio/7.0/LocalInstall/Addins/
```

> [!NOTE]
> Pour plus d’informations sur la désinstallation de Visual Studio pour Mac, consultez [Désinstallation de Visual Studio pour Mac](https://docs.microsoft.com/visualstudio/mac/uninstall) sur docs.microsoft.com.

<a name="uninstallmono" />

### <a name="uninstall-mono-sdk-mdk"></a>Désinstaller le SDK Mono (MDK)

Mono est une implémentation open source du .NET Framework. Il est utilisé par tous les produits Xamarin (Xamarin.iOS, Xamarin.Android et Xamarin.Mac) pour permettre le développement de ces plateformes en C#.

> [!WARNING]
> D’autres applications en dehors de Xamarin utilisent également Mono, par exemple, Unity. Vérifiez qu’il n’existe pas d’autres dépendances de Mono avant de le désinstaller.

Pour supprimer le framework Mono, exécutez les commandes suivantes dans Terminal :

```bash
sudo rm -rf /Library/Frameworks/Mono.framework
sudo pkgutil --forget com.xamarin.mono-MDK.pkg
sudo rm /etc/paths.d/mono-commands
```

<a name="uninstallandroid" />

### <a name="uninstall-xamarinandroid"></a>Désinstaller Xamarin.Android

Un certain nombre d’éléments qui sont obligatoires lors de l’utilisation de Xamarin.Android, comme les kits Android SDK et Java SDK, doivent être supprimés lors de la désinstallation de Xamarin.Android. Cette section vous guide dans la désinstallation de tous les composants nécessaires.

Pour supprimer Xamarin.Android, exécutez les commandes suivantes dans Terminal :

```bash
sudo rm -rf /Developer/MonoDroid
rm -rf ~/Library/MonoAndroid
sudo pkgutil --forget com.xamarin.android.pkg
sudo rm -rf /Library/Frameworks/Xamarin.Android.framework
```

#### <a name="uninstall-android-sdk-and-java-sdk"></a>Désinstaller Android SDK et le Java SDK

Android SDK est nécessaire pour le développement d’applications Android. Pour supprimer complètement toutes les composants d’Android SDK, recherchez le fichier **~/Library/Developer/Xamarin/** et placez-le dans la **Corbeille**.

Il n’est pas nécessaire de désinstaller le SDK Java (JDK), car il est déjà préempaqueté dans Mac OS X.

#### <a name="uninstall-android-avd"></a>Désinstaller Android AVD

> [!WARNING]
> Il existe d’autres applications en dehors de Visual Studio pour Mac qui utilisent également Android AVD et des composants Android supplémentaires, comme Android Studio.
> La suppression de ce répertoire peut entraîner le fonctionnement incorrect de projets dans Android Studio. 

Pour supprimer tous les AVD Android et d’autres composants Android, utilisez la commande suivante :

```bash
rm -rf ~/.android
```

Pour supprimer seulement les AVD Android, utilisez la commande suivante :

```bash
rm -rf ~/.android/avd
```

<a name="uninstallios" />

### <a name="uninstall-xamarinios"></a>Désinstaller Xamarin.iOS

Xamarin.iOS permet le développement d’applications iOS en C# ou F#. Pour désinstaller Xamarin.iOS d’une machine, suivez les étapes ci-dessous :

Pour supprimer tous les fichiers Xamarin.iOS, utilisez les commandes suivantes dans Terminal :

```bash
rm -rf ~/Library/MonoTouch
sudo rm -rf /Library/Frameworks/Xamarin.iOS.framework
sudo rm -rf /Developer/MonoTouch
sudo pkgutil --forget com.xamarin.monotouch.pkg
sudo pkgutil --forget com.xamarin.xamarin-ios-build-host.pkg
sudo pkgutil --forget com.xamarin.xamarin.ios.pkg
```

<a name="uninstallmac" />

### <a name="uninstall-xamarinmac"></a>Désinstaller Xamarin.Mac

Vous pouvez supprimer Xamarin.Mac de votre machine à l’aide des deux commandes suivantes pour supprimer respectivement le produit et la licence de votre Mac :

```bash
sudo rm -rf /Library/Frameworks/Xamarin.Mac.framework
rm -rf ~/Library/Xamarin.Mac
```

<a name="uninstallworkbooks" />

### <a name="uninstall-workbooks"></a>Désinstaller Workbooks

Pour supprimer Xamarin Workbooks 1.2.2 (ou une version ultérieure), utilisez les commandes suivantes dans le terminal :

```bash
sudo /Library/Frameworks/Xamarin.Interactive.framework/Versions/Current/uninstall
```

Pour les versions antérieures, consultez le guide de désinstallation de [Workbooks](~/tools/workbooks/install.md#uninstall-macos).

<a name="uninstallprofiler" />

### <a name="uninstall-the-xamarin-profiler"></a>Désinstallez Xamarin Profiler

Pour supprimer Xamarin Profiler, utilisez les commandes suivantes dans Terminal :

```bash
sudo rm -rf "/Applications/Xamarin Profiler.app"
```

<a name="uninstallinstaller" />

### <a name="uninstall-the-xamarin-installer"></a>Désinstaller Xamarin Installer

Pour supprimer toutes les traces de Xamarin Universal Installer, utilisez les commandes suivantes :

```bash
rm -rf ~/Library/Caches/XamarinInstaller/
rm -rf ~/Library/Caches/VisualStudioInstaller/
rm -rf ~/Library/Logs/XamarinInstaller/
rm -rf ~/Library/Logs/VisualStudioInstaller/
rm -rf ~/Library/Preferences/Xamarin/
rm -rf "~/Library/Preferences/Visual Studio/"
```

<a name="uninstallscript" />

### <a name="using-the-uninstall-script"></a>Utilisation du script de désinstallation

> [!TIP]
> Avant d’exécuter un script que vous téléchargez, veuillez lire les commandes pour comprendre les modifications apportées à votre ordinateur.

[Ce script de désinstallation](https://raw.githubusercontent.com/MicrosoftDocs/visualstudio-docs/master/mac/resources/uninstall-vsmac.sh) vous permet de désinstaller Visual Studio pour Mac et les composants Xamarin associés en un seul Go.

Ce script contient la plupart des commandes que vous trouvez dans l’article. Il y a deux omissions principales du script et ne sont pas incluses en raison de dépendances externes possibles (d’autres applications peuvent utiliser mono ou les outils Android) :

- Désinstallation de Mono
- Désinstallation d’Android AVD

Pour exécuter le script :

1. Cliquez avec le bouton droit sur [ce lien pour **Uninstall-vsmac.sh** ](https://raw.githubusercontent.com/MicrosoftDocs/visualstudio-docs/master/mac/resources/uninstall-vsmac.sh) et enregistrez/Téléchargez le fichier sur votre Mac.

2. Ouvrez **Terminal**, puis accédez au répertoire de travail où le script a été téléchargé :

    ```bash
    cd /location/of/file
    ```

3. Rendez le script exécutable et exécutez-le avec **sudo** :

    ```bash
    chmod +x ./uninstall-vsmac.sh
    sudo ./uninstall-vsmac.sh
    ```

4. Supprimez le script de désinstallation.

Les outils Visual Studio pour Mac et Xamarin doivent à présent être désinstallés de votre ordinateur.

<a name="uninstallwindows" />

## <a name="uninstalling-xamarin-on-windows"></a>Désinstallation de Xamarin sur un système Windows

Xamarin est pris en charge sur ces IDE :

- [Visual Studio 2019 et Visual Studio 2017](#uninstallvs2017)
- [Visual Studio 2015](#uninstallvs2015)
- [Visual Studio 2013](#uninstallvs2015) [**Non pris en charge**]
- [Xamarin Studio](#uninstallxamarinstudio) [**Non pris en charge**]

<a name="uninstallvs2017" />

### <a name="visual-studio-2019-and-visual-studio-2017"></a>Visual Studio 2019 et Visual Studio 2017

Xamarin est désinstallé de Visual Studio 2019 et de Visual Studio 2017 à l’aide de l’application du programme d’installation :

1. Utilisez le **menu Démarrer** pour ouvrir **Visual Studio Installer**.

2. Appuyez sur le bouton **Modifier** pour l’instance que vous souhaitez modifier.

    [![](uninstalling-xamarin-images/vs2017-02-sml.png "Press the modify button")](uninstalling-xamarin-images/vs2017-02.png#lightbox)

3. Sous l’onglet **Charges de travail**, décochez l’option  **Développement mobile en .NET** (dans la section **Mobile et jeux**).

    [![](uninstalling-xamarin-images/vs2017-03-sml.png "Uncheck the Mobile Development workload")](uninstalling-xamarin-images/vs2017-03.png#lightbox)

4. Cliquez sur le bouton **Modifier** situé en bas à droite de la fenêtre.

5. Visual Studio Installer va supprimer les composants qui ont été désélectionnés (Visual Studio 2017 doit être fermé pour que Visual Studio Installer puisse apporter les modifications nécessaires).

    [![](uninstalling-xamarin-images/vs2017-04-sml.png "Press the Modify button")](uninstalling-xamarin-images/vs2017-04.png#lightbox)

Pour supprimer les composants Xamarin un à un (par exemple, Profiler ou Workbooks), accédez à l’onglet **Composants individuels** à l’étape 3, puis décochez les composants de votre choix :

[![](uninstalling-xamarin-images/vs2017-components-sml.png "Uninstall individual components")](uninstalling-xamarin-images/vs2017-components.png#lightbox)

Pour désinstaller entièrement Visual Studio 2017, choisissez **Désinstaller** dans le menu en regard du bouton **Lancer** (représenté par trois barres superposées).

[![](uninstalling-xamarin-images/vs2017-uninstall-sml.png "Uninstall Visual Studio completely")](uninstalling-xamarin-images/vs2017-uninstall.png#lightbox)

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

## <a name="uninstalling-older-and-unsupported-products"></a>Désinstallation des produits plus anciens et non pris en charge

<a name="uninstallvs2015"></a>

### <a name="visual-studio-2015-and-earlier"></a>Visual Studio 2015 et versions antérieures

Pour désinstaller intégralement Visual Studio 2015, utilisez [la réponse du support sur visualstudio.com](https://visualstudio.microsoft.com/vs/support/vs2015/uninstall-visual-studio-2015/).

Sur les ordinateurs Windows, Xamarin peut être désinstallé via le **Panneau de configuration**. Accédez à **Programmes et fonctionnalités** ou à **Programmes > Désinstaller un programme**, comme illustré ci-dessous :

 [![](uninstalling-xamarin-images/image3.png "Navigate to Programs and Features or Programs  Uninstall a Program as illustrated here")](uninstalling-xamarin-images/image3.png#lightbox) 

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

<a name="uninstallxamarinstudio"></a>

### <a name="uninstall-xamarin-studio-on-windows"></a>Désinstaller Xamarin Studio sur Windows

Sur les ordinateurs Windows, Xamarin est désinstallé via le **Panneau de configuration**. Accédez à **Programmes et fonctionnalités** ou à **Programmes > Désinstaller un programme**. 

Pour désinstaller Xamarin Studio, recherchez **Xamarin Studio 5.x.x** dans la liste des programmes, puis cliquez sur le bouton **Désinstaller**. 

### <a name="uninstall-xamarin-studio-on-mac"></a>Désinstaller Xamarin Studio sur Mac

Pour désinstaller Xamarin Studio sur un système Mac, la première chose à faire est de localiser **Xamarin Studio.app** dans le répertoire **/Applications**, puis de le faire glisser dans la **Corbeille**. Vous pouvez aussi cliquer avec le bouton droit et sélectionner **Placer dans la Corbeille**, comme illustré ci-dessous :

 [![](uninstalling-xamarin-images/image1.png "Alternatively, right-click and select Move to Trash as illustrated here")](uninstalling-xamarin-images/image1.png#lightbox)

La suppression de ce bundle d’applications a pour effet de désinstaller Xamarin Studio. Toutefois, d’autres fichiers relatifs à Xamarin peuvent subsister dans le système de fichiers.

Pour supprimer toutes les traces de Xamarin Studio, les commandes suivantes doivent être exécutées dans Terminal :

```bash
sudo rm -rf "/Applications/Xamarin Studio.app"
rm -rf ~/Library/Caches/XamarinStudio-*
rm -rf ~/Library/Preferences/XamarinStudio-*
rm -rf ~/Library/Logs/XamarinStudio-*
rm -rf ~/Library/XamarinStudio-*
```

## <a name="summary"></a>Récapitulatif

Cet article a fourni des instructions sur la désinstallation intégrale de Xamarin sur un Mac via l’utilisation des commandes Terminal. Il a également fourni des instructions sur la désinstallation de Xamarin d’une machine Windows via l’option **Programmes et fonctionnalités** (pour Visual Studio 2015 et antérieur) et via **Visual Studio Installer** pour Visual Studio 2017.

## <a name="related-links"></a>Liens associés

- [Désinstaller le script (échantillon)](https://raw.githubusercontent.com/MicrosoftDocs/visualstudio-docs/master/mac/resources/uninstall-vsmac.sh)
