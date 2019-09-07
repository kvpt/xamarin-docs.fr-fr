---
title: Installation et configuration requise des classeurs
description: Ce document décrit comment télécharger et installer Xamarin Workbooks, abordant les plateformes prises en charge et la configuration système requise.
ms.prod: xamarin
ms.assetid: 9D4E10E8-A288-4C6C-9475-02969198C119
author: conceptdev
ms.author: crdun
ms.date: 06/19/2018
ms.openlocfilehash: 4638d599d72ea2a6375aa2afff5974ef8c09308d
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70772119"
---
# <a name="workbooks-installation-and-requirements"></a>Installation et configuration requise des classeurs

<a name="install" />

## <a name="download-and-install"></a>Télécharger et installer

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

1. Vérifiez la [Configuration requise](#requirements) ci-dessous.
2. Téléchargez et installez [Xamarin Workbooks pour Windows](https://dl.xamarin.com/interactive/XamarinInteractive.msi).
3. Commencez à [Parcourir](~/tools/workbooks/workbook.md) les classeurs.

# <a name="macostabmacos"></a>[macOS](#tab/macos)

1. Vérifiez la [Configuration requise](#requirements) ci-dessous.
2. Téléchargez et installez [Xamarin Workbooks pour Mac](https://dl.xamarin.com/interactive/XamarinInteractive.pkg).
3. Commencez [à](~/tools/workbooks/workbook.md)vous lancer.

-----

## <a name="requirements"></a>Configuration requise

#### <a name="supported-operating-systems"></a>Systèmes d'exploitation pris en charge

- **Mac** -OS X 10,11 ou version ultérieure
- **Windows** -Windows 7 ou version ultérieure (avec Internet Explorer 11 ou version ultérieure et .net 4.6.1 ou version ultérieure)

#### <a name="supported-app-platforms"></a>Plateformes d’application prises en charge

|Plateforme d’application|Prise en charge du système d’exploitation|Notes|
|--- |--- |--- |
|Mac|Pris en charge uniquement sur Mac|
|iOS|Pris en charge sur Mac et Windows|Xamarin. iOS 11,0 et Xcode 9,0 ou version ultérieure doivent être installés sur Mac. L’exécution de classeurs iOS sur Windows requiert un hôte de build Mac exécutant tous les éléments ci-dessus, et le [simulateur iOS distant](~/tools/ios-simulator/index.md) installé sur Windows.|
|Android|Pris en charge sur Mac et Windows|Nécessite l’utilisation de Google, de Visual Studio ou de l’émulateur Xamarin Android, avec un appareil virtuel > = 5,0|
|WPF|Pris en charge uniquement sur Windows|
|Console (.NET Framework)|Pris en charge sur Mac et Windows|
|Console (.NET Core)|Pris en charge sur Mac et Windows|

## <a name="reporting-bugs"></a>Signalement des bogues

[Signalez les problèmes sur GitHub][bugs]et incluez toutes les informations suivantes :

### <a name="log-files"></a>Fichiers journaux

Toujours joindre les classeurs des fichiers journaux du client :

- Macintosh`~/Library/Logs/Xamarin/Workbooks/Xamarin Workbooks {date}.log`
- Windows : `%LOCALAPPDATA%\Xamarin\Workbooks\logs\Xamarin Workbooks {date}.log`

1.4. x offre également la possibilité de sélectionner le fichier journal dans Finder (macOS) ou Explorer (Windows) directement à partir du menu principal :

- **Aide > Afficher le fichier journal**

#### <a name="log-paths-for-workbooks-13-and-earlier"></a>Chemins d’accès de journal pour les classeurs 1,3 et versions antérieures :

- Macintosh`~/Library/Logs/Xamarin/Inspector/Xamarin Inspector {date}.log`
- Windows : `%LOCALAPPDATA%\Xamarin\Inspector\logs\Xamarin Inspector {date}.log`

### <a name="platform-version-information"></a>Informations sur la version de la plateforme

Il est très utile de connaître les détails sur votre système d’exploitation et les produits Xamarin installés.

Dans le menu principal de classeurs :

- **Aide > copier les informations de version**

#### <a name="instructions-for-workbooks-13-and-earlier"></a>Instructions pour les classeurs 1,3 et versions antérieures :

Visual Studio pour Mac

- **Visual Studio > à propos de Visual Studio > Afficher les détails > copier les informations**
- Coller dans le rapport de bogue

Visual Studio

- **Aide > sur Visual Studio > copier les informations**
- Indiquez-nous la version de votre système d’exploitation et indiquez si vous exécutez Windows 32 bits ou 64 bits.

### <a name="samples"></a>Exemples

Si vous êtes en mesure de joindre le fichier **. classeurs** ou de créer un lien vers celui-ci, cela peut vous aider à résoudre votre bogue plus rapidement.

### <a name="devices"></a>Appareils

Si vous rencontrez des problèmes de connexion à votre classeur iOS ou Android et que vous avez déjà consulté [notre page de dépannage](~/tools/workbooks/troubleshooting/index.md), nous devrons connaître les éléments suivants :

- Nom de l’appareil auquel vous essayez de vous connecter
- Version du système d’exploitation de votre appareil
- Tablette Vérifier que vous utilisez un émulateur x86
- Tablette Quelle plateforme d’émulateur utilisez-vous ? Émulateur Google ?
  Visual Studio Émulateur Android ? Xamarin Android Player ?
- iOS sur Windows : Quelle version du simulateur iOS Xamarin distante avez-vous installé (sélectionnez **Ajout/suppression de programmes** dans le **panneau de configuration**) ?
- iOS sur Windows : Fournissez également les informations de version de la plateforme pour votre hôte de build Mac
- L’appareil dispose-t-il d’une connectivité réseau (Vérifiez via le navigateur Web) ?

[bugs]: https://github.com/Microsoft/workbooks/issues/new

## <a name="uninstall"></a>Désinstaller l’interface

### <a name="windows"></a>Windows

Selon la manière dont vous avez acquis les classeurs, vous devrez peut-être effectuer deux procédures de désinstallation. Vérifiez les deux à la fois pour désinstaller complètement le logiciel.

#### <a name="visual-studio-installer"></a>Programme d’installation de Visual Studio

Si vous disposez de Visual Studio 2017, ouvrez **Visual Studio installer**et recherchez dans **les composants individuels** des **Xamarin Workbooks**. Si elle est cochée, décochez-la, puis cliquez sur **modifier** pour désinstaller.

#### <a name="system-uninstall"></a>Désinstallation du système

Si vous avez installé des classeurs avec un programme d’installation téléchargé, vous devez les désinstaller via la page des paramètres système des **applications & fonctionnalités** sur Windows 10 ou via **Ajout/suppression de programmes** dans le panneau de configuration sur les versions antérieures de Windows.

> **Démarrer les paramètres > > système > applications &**

![](install-images/windows-remove.png "Xamarin Workbooks comme indiqué dans &quot;les &amp; fonctionnalités des applications&quot;")

**Vous devez toujours suivre la procédure de la Visual Studio Installer pour vous assurer que les classeurs ne sont pas réinstallés à votre insu.**

<a name="uninstall-macos" />

### <a name="macos"></a>macOS

À compter de la [1.2.2](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/interactive/interactive-1.2.md), Xamarin Workbooks peut être désinstallé d’un terminal en exécutant :

```bash
sudo /Library/Frameworks/Xamarin.Interactive.framework/Versions/Current/uninstall
```

Le programme de désinstallation va détailler les fichiers et les répertoires qu’il va supprimer et demander confirmation avant de continuer.

Transmettez `-help` l’argument `uninstall` au script pour des scénarios plus avancés.

Pour les versions antérieures, vous devez supprimer manuellement les éléments suivants :

1. Supprimez l’application Workbooks dans`"/Applications/Xamarin Workbooks.app"`
2. Supprimez l’application Inspector dans`"Applications/Xamarin Inspector.app"`
3. Supprimez les compléments : `"~/Library/Application Support/XamarinStudio-6.0/LocalInstall/Addins/Xamarin.Interactive"` et `"~/Library/Application Support/XamarinStudio-6.0/LocalInstall/Addins/Xamarin.Inspector"`
4. Supprimez Inspector et les fichiers de prise en charge ici : `/Library/Frameworks/Xamarin.Interactive.framework` et `/Library/Frameworks/Xamarin.Inspector.framework`

## <a name="downgrading"></a>Rétrogradation

Identificateur de Bundle pour les **classeurs/applications/Xamarin. l’application** a `com.xamarin.Inspector` été `com.xamarin.Workbooks` remplacée par dans la version 1,4, car les classeurs et Inspector sont maintenant entièrement fractionnés.

En raison d’un bogue dans les programmes d’installation plus anciens, il n’est pas possible de rétrograder 1,4 ou des versions plus récentes à l’aide des programmes d’installation 1.3.2 ou antérieurs.

Pour passer à une version antérieure de 1,4 ou ultérieure à 1.3.2 ou une version antérieure :

1. [Désinstaller manuellement des classeurs & Inspector](#uninstall-macos)
2. Exécutez le programme d’installation `.pkg` 1.3.2 ou une version antérieure
