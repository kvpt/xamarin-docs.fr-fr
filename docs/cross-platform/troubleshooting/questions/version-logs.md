---
title: Où trouver les informations sur ma version et les journaux ?
description: Ce document explique où rechercher les informations de version et les journaux Xamarin. Ces informations sont utiles pour diagnostiquer des problèmes, envoyer des bogues ou obtenir un support.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: CF386485-EAB0-4B9E-AA17-CB1B6462E505
author: davidortinau
ms.author: daortin
ms.date: 03/29/2017
ms.openlocfilehash: 997c6398c4cd9c4f4be6fbcd60847d82b0cae13d
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457833"
---
# <a name="where-can-i-find-my-version-information-and-logs"></a>Où trouver les informations sur ma version et les journaux ?

## <a name="outline"></a>Contour

- [Informations sur la version](#version-information)
  - Informations sur la version de Windows
  - Informations sur la version Mac
  - Android SDK Tools, outils de plateforme, outils de génération
- [Journaux de l’IDE et du programme d’installation](#ide-and-installer-logs)
  - [Journaux Windows](#windows-logs)
    - Xamarin Studio
    - Xamarin pour Visual Studio
    - Programme d’installation Xamarin Universal
    - `.msi`Programmes d’installation individuels, journaux détaillés
    - Démarrage de Visual Studio, journaux détaillés
  - [Journaux Mac](#mac-logs)
    - Hôte de build
  - Visual Studio pour Mac
    - Xamarin Studio
    - Programme d’installation de Xamarin
- [Sortie de la génération détaillée](#verbose-build-output-logs)
- [Journaux de débogage pour les applications Xamarin. Android et Xamarin. iOS](#debug-logs-for-xamarin-apps)
  - `adb`Journaux logcat Android
  - journaux du simulateur iOS (sur Mac)
  - journaux des appareils iOS (sur Mac)

## <a name="version-information"></a><a id="version-information" name="version-information" />Informations sur la version

Il est généralement préférable de renvoyer toutes les informations à partir des boutons **copier les informations** . Dans le cas contraire, nous aurons souvent besoin de demander des informations supplémentaires. Par exemple, les versions de système d’exploitation, la version de Xcode, les niveaux d’API Android installés et la version .NET peuvent toutes être importantes lorsque vous aidez à résoudre un problème.

### <a name="windows-version-information"></a><a id="windows-version-information" name="windows-version-information" />Informations sur la version de Windows

#### <a name="xamarin-studio"></a>Xamarin Studio

**Aide > sur > afficher les détails > copier les informations [Button]**

#### <a name="visual-studio"></a>Visual Studio

**Aide > sur Microsoft Visual Studio > copier les informations [Button]**

### <a name="mac-version-information"></a><a id="mac-version-information" name="mac-version-information" />Informations sur la version Mac

#### <a name="visual-studio-for-mac"></a>Visual Studio pour Mac

**Visual Studio > à propos de Visual Studio > afficher les détails > copier les informations [Button]**

### <a name="android-sdk-tools-platform-tools-build-tools"></a><a id="android-sdk-tools-versions" name="android-sdk-tools-versions" />Android SDK Tools, outils de plateforme, outils de génération

Ouvrez le gestionnaire de Android SDK et prenez une capture d’écran de la section **Outils** Top.

#### <a name="visual-studio-for-mac"></a>Visual Studio pour Mac

**Outils > ouvrir Android SDK Manager**

#### <a name="visual-studio"></a>Visual Studio

**Outils > Android > ouvrir Android SDK Manager...**

## <a name="ide-and-installer-logs"></a><a id="ide-and-installer-logs" name="ide-and-installer-logs" />Journaux de l’IDE et du programme d’installation

Pour chaque emplacement de journalisation, assurez-vous de procéder à l’installation et à l’attachement de l’intégralité du dossier de journal.

### <a name="windows-logs"></a><a id="windows-logs" name="windows-logs" />Journaux Windows

#### <a name="visual-studio-tools-for-xamarin"></a><a id="windows-logs-xamarin-vs" name="windows-logs-xamarin-vs" />Visual Studio Tools pour Xamarin

`%LOCALAPPDATA%\Xamarin\Logs`

#### <a name="visual-studio-2017"></a><a id="vs-2017" name="vs-2017" /> Visual Studio 2017

[Guide pratique pour obtenir les journaux d’installation Visual Studio](/visualstudio/install/troubleshooting-installation-issues#how-to-get-the-visual-studio-installation-logs)

#### <a name="visual-studio-2015"></a><a id="vs-2015" name="vs-2015" /> Visual Studio 2015

#### <a name="xamarin-universal-installer"></a><a id="windows-universal-installer" name="windows-universal-installer" /> Programme d’installation de Xamarin « Universal »

`%LOCALAPPDATA%\Xamarin\Universal`

Il s’agit des journaux du `XamarinInstaller.exe` programme d’installation.

#### <a name="individual-msi-installers-verbose-logs"></a><a id="individual-msi-installers-verbose-logs" name="individual-msi-installers-verbose-logs" />`.msi`Programmes d’installation individuels, journaux détaillés

```csharp
msiexec /i Xamarin.msi /l*vx "%USERPROFILE%\Desktop\Xamarin.log"
```

Référence : [options de ligne de commande](/windows/win32/msi/command-line-options)

#### <a name="visual-studio-startup-verbose-logs"></a><a id="visual-studio-startup-verbose-logs" name="visual-studio-startup-verbose-logs" />Démarrage de Visual Studio, journaux détaillés

```csharp
devenv.exe /log "%USERPROFILE%\Desktop\VisualStudio.log"
```

Référence : [/log (devenv.exe)](/visualstudio/ide/reference/log-devenv-exe)

### <a name="mac-logs"></a><a id="mac-logs" name="mac-logs" />Journaux Mac

Vous pouvez sélectionner l’élément de menu **go > accéder au dossier** dans Finder, puis copier et coller l’un de ces chemins dans la boîte de dialogue.

#### <a name="visual-studio-for-mac"></a><a id="mac-logs-visual-studio" name="mac-logs-visual-studio" />Visual Studio pour Mac

`~/Library/Logs/VisualStudio/7.0` (ce nombre peut varier selon la version que vous utilisez)

Ce dossier peut également être ouvert via « aide-> ouvrir le répertoire des journaux ».

#### <a name="xamarin-studio"></a><a id="mac-logs-xamarin-studio" name="mac-logs-xamarin-studio" />Xamarin Studio

`~/Library/Logs/XamarinStudio-6.0` (ce nombre peut varier selon la version que vous utilisez)

Ce dossier peut également être ouvert via « aide-> ouvrir le répertoire des journaux ».

#### <a name="xamarin-universal-installer"></a><a id="mac-universal-installer" name="mac-universal-installer" />Programme d’installation de Xamarin « Universal »

`~/Library/Logs/XamarinInstaller/Universal`

Il s’agit des journaux du `XamarinInstaller.dmg` programme d’installation.

#### <a name="xamarin-build-host"></a><a id="mac-build-host" name="mac-build-host" />Hôte de build Xamarin

`~/Library/Logs/Xamarin-[MAJOR.MINOR]`

## <a name="verbose-build-output"></a><a id="verbose-build-output-logs" name="verbose-build-output-logs" />Sortie de la génération détaillée

1. Activez le diagnostic de la [sortie MSBuild](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output).

2. Pour les applications iOS, activez également la **sortie verbose mTouch** en ajoutant `-v -v -v -v` sous **Propriétés du projet > Build iOS > général (onglet) > options supplémentaires > arguments mTouch supplémentaires**.

3. Nettoyez et régénérez le projet.

4. Copiez et collez la sortie de génération de l’IDE dans un fichier texte.
     - Visual Studio (Windows) : **afficher > sortie > afficher la sortie à partir de : Build**
     - Visual Studio pour Mac : **afficher les > pad > les erreurs > sortie de génération (onglet)**

## <a name="debug-logs-for-xamarinandroid-and-xamarinios-apps"></a><a id="debug-logs-for-xamarin-apps" name="debug-logs-for-xamarin-apps" />Journaux de débogage pour les applications Xamarin. Android et Xamarin. iOS

### <a name="visual-studio-for-mac"></a>Visual Studio pour Mac

**Afficher > Pad > sortie de l’application**

(Notez que cet élément de menu n’apparaît que lorsque l’application a été lancée.)

### <a name="visual-studio"></a>Visual Studio

**Afficher > sortie > afficher la sortie à partir de : débogage**

### <a name="android-adb-logcat-logs"></a><a id="adb-logcat" name="adb-logcat" />[`adb`](https://developer.android.com/tools/help/adb.html)Journaux logcat Android

Après avoir exécuté la `adb` commande, rattachez le fichier **android_logcat.txt** à partir de votre bureau. Ces instructions supposent que vous n’avez qu’un seul appareil attaché.

Consultez également la page [Journal de débogage Android](~/android/deploy-test/debugging/android-debug-log.md) .

#### <a name="visual-studio"></a>Visual Studio

1. **Outils > Android > démarrer l’invite de commandes Android ADB**
2. Nettoyer le journal : `adb logcat -c`
3. Reproduisez le problème.
4. Sortie du journal : `adb logcat -vtime -d > "%USERPROFILE%\Desktop\android_logcat.txt"`

#### <a name="visual-studio-for-mac"></a>Visual Studio pour Mac

1. **Outils > ouvrir Android SDK invite de commandes**
2. Nettoyer le journal : `adb logcat -c`
3. Reproduisez le problème.
4. Sortie du journal : `adb logcat -vtime -d > ~/Desktop/android_logcat.txt`

### <a name="ios-simulator-logs-on-mac"></a><a id="ios-simulator-logs" name="ios-simulator-logs" />journaux du simulateur iOS (sur Mac)

- Pour accéder au journal système, sélectionnez **Déboguer > ouvrir le journal système** dans l’application iOS Simulator.

- Pour afficher les rapports d’incident à partir du simulateur, ouvrez console. app et accédez à `~/Library/Logs > DiagnosticReports` .

### <a name="ios-device-logs-on-mac"></a><a id="ios-device-logs" name="ios-device-logs" />journaux des appareils iOS (sur Mac)

#### <a name="visual-studio-for-mac"></a>Visual Studio pour Mac

**Afficher > Pad > journal de l’appareil iOS**

#### <a name="xcode"></a>Xcode

**Appareils Windows > > $ {DeviceName}**

Les rapports d’incidents sont disponibles sous le bouton **afficher les journaux** de l’appareil. Le journal système de l’appareil s’affiche au bas de la fenêtre sous la flèche de diffusion.

#### <a name="xcode-5"></a>Xcode 5

**Windows > Organizer > appareils (onglet) > $ {DeviceName}**