---
title: Configuration requise et Installation de workbooks
description: Ce document décrit comment télécharger et installer Xamarin Workbooks, abordant la configuration système requise et plates-formes prises en charge.
ms.prod: xamarin
ms.assetid: 9D4E10E8-A288-4C6C-9475-02969198C119
author: lobrien
ms.author: laobri
ms.date: 06/19/2018
ms.openlocfilehash: cb79323c416c107c35c465bc2a8815ab267cc451
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67865942"
---
# <a name="workbooks-installation-and-requirements"></a>Configuration requise et Installation de workbooks

<a name="install" />

## <a name="download-and-install"></a>Téléchargez et installez

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

1. Vérifier le [exigences](#requirements) ci-dessous.
2. Téléchargez et installez [Xamarin classeurs pour Windows](https://dl.xamarin.com/interactive/XamarinInteractive.msi).
3. Démarrer [à toucher](~/tools/workbooks/workbook.md) avec des classeurs ou essayer la [exemples](https://developer.xamarin.com/workbooks)

# <a name="macostabmacos"></a>[macOS](#tab/macos)

1. Vérifier le [exigences](#requirements) ci-dessous.
2. Téléchargez et installez [Xamarin Workbooks pour Mac](https://dl.xamarin.com/interactive/XamarinInteractive.pkg).
3. Démarrer [à toucher](~/tools/workbooks/workbook.md) avec des classeurs ou essayer la [exemples](https://developer.xamarin.com/workbooks)

-----

## <a name="requirements"></a>Configuration requise

#### <a name="supported-operating-systems"></a>Systèmes d'exploitation pris en charge

- **Mac** -OS X 10.11 ou supérieure
- **Windows** -Windows 7 ou version ultérieure (avec Internet Explorer 11 ou ultérieur et de .NET 4.6.1 ou version ultérieure)

#### <a name="supported-app-platforms"></a>Plateformes d’applications pris en charge

|Plateforme d’application|Prise en charge du système d’exploitation|Notes|
|--- |--- |--- |
|Mac|Uniquement pris en charge sur Mac|
|iOS|Prise en charge sur Mac et Windows|Xamarin.iOS 11.0 et Xcode 9.0 ou version ultérieure doivent être installé sur le Mac. IOS classeurs en cours d’exécution sur Windows nécessite un hôte de build Mac exécutant tous les éléments ci-dessus et le [Remoted iOS Simulator](~/tools/ios-simulator/index.md) installé sur Windows.|
|Android|Prise en charge sur Mac et Windows|Doit utiliser émulateur Google, Visual Studio ou Xamarin Android, avec un appareil virtuel > = 5.0|
|WPF|Uniquement pris en charge sur Windows|
|Console (.NET Framework)|Prise en charge sur Mac et Windows|
|Console (.NET Core)|Prise en charge sur Mac et Windows|


## <a name="reporting-bugs"></a>Signalement des bogues

Veuillez [signaler des problèmes sur GitHub][bugs]et inclure toutes les informations suivantes :

### <a name="log-files"></a>Fichiers journaux

Attachez toujours les fichiers journaux du client classeurs :

- Mac : `~/Library/Logs/Xamarin/Workbooks/Xamarin Workbooks {date}.log`
- Windows : `%LOCALAPPDATA%\Xamarin\Workbooks\logs\Xamarin Workbooks {date}.log`

1.4.x comprend également la possibilité de sélectionner le fichier journal dans Finder (macOS) ou l’Explorateur (Windows) directement dans le menu principal :

- **Aide > Afficher le fichier journal**

#### <a name="log-paths-for-workbooks-13-and-earlier"></a>Journal des chemins d’accès pour les classeurs 1.3 et versions antérieures :

- Mac : `~/Library/Logs/Xamarin/Inspector/Xamarin Inspector {date}.log`
- Windows : `%LOCALAPPDATA%\Xamarin\Inspector\logs\Xamarin Inspector {date}.log`

### <a name="platform-version-information"></a>Informations de Version de plateforme

Il est très utile de connaître les détails sur votre système d’exploitation et les produits Xamarin installés.

Dans le menu principal dans les classeurs :

* **Aide > Copier les informations de Version**

#### <a name="instructions-for-workbooks-13-and-earlier"></a>Instructions pour les classeurs 1.3 et versions antérieures :

Visual Studio pour Mac

- **Visual Studio > à propos de Visual Studio > Afficher les détails > Copier les informations**
- Collez dans un rapport de bogues

Visual Studio

- **Aide > à propos de Visual Studio > Copier les informations**
- Donnez-nous votre version de système d’exploitation et que vous exécutez Windows 32 bits ou 64 bits.

### <a name="samples"></a>Exemples

Si vous pouvez attacher ou créer un lien vers le **.workbooks** fichier que vous rencontrez des problèmes avec, et qui peut-être aider à résoudre le bogue plus rapidement.

### <a name="devices"></a>Appareils

Si vous rencontrez des problèmes de connexion de votre classeur Android ou iOS et déjà archivées [notre page de dépannage](~/tools/workbooks/troubleshooting/index.md), nous devons savoir :

- Nom du périphérique que vous essayez de vous connecter à
- Version du système d’exploitation de votre appareil
- Android : Vérifiez que vous utilisez un x86 émulateur
- Android : Quelle plate-forme émulateur utilisez-vous ? Émulateur Google ?
  Émulateur Android de Visual Studio ? Xamarin Android Player ?
- iOS sur Windows : Quelle version du simulateur iOS à distance Xamarin vous avez installé (vérifier **Ajout/Suppression de programmes** dans **le panneau de configuration**) ?
- iOS sur Windows : Fournissez également les informations de Version de plateforme pour votre hôte de build Mac
- L’appareil a-t-il la connectivité réseau (vérification via le navigateur web) ?

[bugs]: https://github.com/Microsoft/workbooks/issues/new

## <a name="uninstall"></a>Désinstaller l’interface

### <a name="windows"></a>Windows

Selon la façon dont vous avez acquis des classeurs, vous devrez exécuter deux procédures de désinstallation. Vérifiez ces deux éléments pour complètement désinstaller le logiciel.

#### <a name="visual-studio-installer"></a>Programme d’installation de Visual Studio

Si vous avez Visual Studio 2017, ouvrez **le programme d’installation de Visual Studio**, puis en regardant dans **composants individuels** pour **Xamarin Workbooks**. Si elle est activée, désactivez-la, puis **modifier** à désinstaller.

#### <a name="system-uninstall"></a>Désinstallation du système

Si vous avez installé les classeurs vous-même avec un programme d’installation téléchargé, elle devra être désinstallé le **applications et fonctionnalités** page des paramètres système sur Windows 10 ou via **Ajout/Suppression de programmes** dans le contrôle Panneau de configuration sur les versions antérieures de Windows.

> **Démarrer > Paramètres > système > applications et fonctionnalités**

![](install-images/windows-remove.png "Comme indiqué dans les classeurs Xamarin &quot;applications &amp; fonctionnalités&quot;")

**Vous devez toujours suivre la procédure pour Visual Studio Installer pour vous assurer de ne pas réinstallé classeurs sans même vous prévenir.**

<a name="uninstall-macos" />

### <a name="macos"></a>macOS

En commençant par [1.2.2](https://developer.xamarin.com/releases/interactive/interactive-1.2/), Xamarin Workbooks peuvent être désinstallés à partir d’un terminal en exécutant :

```bash
sudo /Library/Frameworks/Xamarin.Interactive.framework/Versions/Current/uninstall
```

Le programme de désinstallation décrit en détail les fichiers et répertoires, elle supprime et demander confirmation avant de continuer.

Passer le `-help` l’argument de la `uninstall` script pour des scénarios plus avancés.

Pour les versions antérieures, vous devez supprimer manuellement les éléments suivants :

1. Supprimez l’application Workbooks dans`"/Applications/Xamarin Workbooks.app"`
2. Supprimez l’application Inspector dans`"Applications/Xamarin Inspector.app"`
3. Supprimez les compléments : `"~/Library/Application Support/XamarinStudio-6.0/LocalInstall/Addins/Xamarin.Interactive"` et `"~/Library/Application Support/XamarinStudio-6.0/LocalInstall/Addins/Xamarin.Inspector"`
4. Supprimez Inspector et les fichiers de prise en charge ici : `/Library/Frameworks/Xamarin.Interactive.framework` et `/Library/Frameworks/Xamarin.Inspector.framework`

## <a name="downgrading"></a>La rétrogradation

L’identificateur de bundle pour **Applications/Xamarin Workbooks.app** a été remplacée par `com.xamarin.Inspector` à `com.xamarin.Workbooks` dans la version 1.4, en tant que classeurs et l’inspecteur sont maintenant entièrement séparé.

En raison d’un bogue dans les programmes d’installation antérieures, il n’est pas possible de rétrograder les versions 1.4 ou ultérieure à l’aide de la 1.3.2 ou programmes d’installation plus anciens.

Pour passer de 1,4 ou plus récent à 1.3.2 ou une version antérieure :

1. [Désinstaller Workbooks & Inspector manuellement](#uninstall-macos)
2. Exécutez le 1.3.2 ou une version antérieure `.pkg` programme d’installation
