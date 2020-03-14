---
title: Où trouver les informations sur ma version et les journaux ?
description: Ce document explique où rechercher les informations de version et les journaux Xamarin. Ces informations sont utiles pour diagnostiquer des problèmes, envoyer des bogues ou obtenir un support.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: CF386485-EAB0-4B9E-AA17-CB1B6462E505
author: davidortinau
ms.author: daortin
ms.date: 03/29/2017
ms.openlocfilehash: 68de58f499788d803aa0af6c68f20e2265b1d6b5
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79304344"
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
    - Programmes d’installation de `.msi` individuel, journaux détaillés
    - Démarrage de Visual Studio, journaux détaillés
  - [Journaux Mac](#mac-logs)
    - Hôte de build
  - Visual Studio pour Mac
    - Xamarin Studio
    - Programme d’installation de Xamarin
- [Sortie de la génération détaillée](#verbose-build-output-logs)
- [Journaux de débogage pour les applications Xamarin. Android et Xamarin. iOS](#debug-logs-for-xamarin-apps)
  - Journaux logcat Android `adb`
  - journaux du simulateur iOS (sur Mac)
  - journaux des appareils iOS (sur Mac)

## <a name="a-idversion-information-nameversion-information-version-information"></a>informations sur la version de <a id="version-information" name="version-information" />

Il est généralement préférable de renvoyer toutes les informations à partir des boutons **copier les informations** . Dans le cas contraire, nous aurons souvent besoin de demander des informations supplémentaires. Par exemple, les versions de système d’exploitation, la version de Xcode, les niveaux d’API Android installés et la version .NET peuvent toutes être importantes lorsque vous aidez à résoudre un problème.

### <a name="a-idwindows-version-information-namewindows-version-information-windows-version-information"></a>informations sur la version de Windows <a id="windows-version-information" name="windows-version-information" />

#### <a name="xamarin-studio"></a>Xamarin Studio

**Aide > sur > Afficher les détails > copier les informations [Button]**

#### <a name="visual-studio"></a>Visual Studio

**Aide > sur Microsoft Visual Studio > copier les informations [Button]**

### <a name="a-idmac-version-information-namemac-version-information-mac-version-information"></a><a id="mac-version-information" name="mac-version-information" />les informations sur la version Mac

#### <a name="visual-studio-for-mac"></a>Visual Studio pour Mac

**Visual Studio > à propos de Visual Studio > Afficher les détails > copier les informations [Button]**

### <a name="a-idandroid-sdk-tools-versions-nameandroid-sdk-tools-versions-android-sdk-tools-platform-tools-build-tools"></a><a id="android-sdk-tools-versions" name="android-sdk-tools-versions" />Android SDK Tools, Platform-Tools, Build-Tools

Ouvrez le gestionnaire de Android SDK et prenez une capture d’écran de la section **Outils** Top.

#### <a name="visual-studio-for-mac"></a>Visual Studio pour Mac

**Outils > Ouvrir Android SDK Manager**

#### <a name="visual-studio"></a>Visual Studio

**Outils > Android > Ouvrir Android SDK Manager...**

## <a name="a-idide-and-installer-logs-nameide-and-installer-logs-ide-and-installer-logs"></a><a id="ide-and-installer-logs" name="ide-and-installer-logs" />les journaux de l’IDE et du programme d’installation

Pour chaque emplacement de journalisation, assurez-vous de procéder à l’installation et à l’attachement de l’intégralité du dossier de journal.

### <a name="a-idwindows-logs-namewindows-logs-windows-logs"></a><a id="windows-logs" name="windows-logs" />les journaux Windows

#### <a name="a-idwindows-logs-xamarin-vs-namewindows-logs-xamarin-vs--visual-studio-tools-for-xamarin"></a>Visual Studio Tools <a id="windows-logs-xamarin-vs" name="windows-logs-xamarin-vs" /> pour Xamarin

`%LOCALAPPDATA%\Xamarin\Logs`

#### <a name="a-idvs-2017-namevs-2017--visual-studio-2017"></a><a id="vs-2017" name="vs-2017" /> Visual Studio 2017

[Obtention des journaux d’installation de Visual Studio](https://docs.microsoft.com/visualstudio/install/troubleshooting-installation-issues#how-to-get-the-visual-studio-installation-logs)

#### <a name="a-idvs-2015-namevs-2015--visual-studio-2015"></a><a id="vs-2015" name="vs-2015" /> Visual Studio 2015

#### <a name="a-idwindows-universal-installer-namewindows-universal-installer--xamarin-universal-installer"></a>programme d’installation de <a id="windows-universal-installer" name="windows-universal-installer" /> Xamarin « Universal »

`%LOCALAPPDATA%\Xamarin\Universal`

Il s’agit des journaux du programme d’installation de `XamarinInstaller.exe`.

#### <a name="a-idindividual-msi-installers-verbose-logs-nameindividual-msi-installers-verbose-logs-individual-msi-installers-verbose-logs"></a><a id="individual-msi-installers-verbose-logs" name="individual-msi-installers-verbose-logs" />des programmes d’installation de `.msi` individuels, journaux détaillés

```csharp
msiexec /i Xamarin.msi /l*vx "%USERPROFILE%\Desktop\Xamarin.log"
```

Référence : [options de ligne de commande](https://msdn.microsoft.com/library/aa367988.aspx)

#### <a name="a-idvisual-studio-startup-verbose-logs-namevisual-studio-startup-verbose-logs-visual-studio-startup-verbose-logs"></a><a id="visual-studio-startup-verbose-logs" name="visual-studio-startup-verbose-logs" />le démarrage de Visual Studio, journaux détaillés

```csharp
devenv.exe /log "%USERPROFILE%\Desktop\VisualStudio.log"
```

Référence : [/log (devenv. exe)](https://msdn.microsoft.com/library/ms241272.aspx)

### <a name="a-idmac-logs-namemac-logs-mac-logs"></a><a id="mac-logs" name="mac-logs" />les journaux Mac

Vous pouvez sélectionner l’élément de menu **go > accéder au dossier** dans Finder, puis copier et coller l’un de ces chemins dans la boîte de dialogue.

#### <a name="a-idmac-logs-visual-studio-namemac-logs-visual-studio-visual-studio-for-mac"></a><a id="mac-logs-visual-studio" name="mac-logs-visual-studio" />Visual Studio pour Mac

`~/Library/Logs/VisualStudio/7.0` (ce nombre peut varier selon la version que vous utilisez)

Ce dossier peut également être ouvert via « aide-> Ouvrir le répertoire des journaux ».

#### <a name="a-idmac-logs-xamarin-studio-namemac-logs-xamarin-studio-xamarin-studio"></a><a id="mac-logs-xamarin-studio" name="mac-logs-xamarin-studio" />Xamarin Studio

`~/Library/Logs/XamarinStudio-6.0` (ce nombre peut varier selon la version que vous utilisez)

Ce dossier peut également être ouvert via « aide-> Ouvrir le répertoire des journaux ».

#### <a name="a-idmac-universal-installer-namemac-universal-installer-xamarin-universal-installer"></a>programme d’installation de <a id="mac-universal-installer" name="mac-universal-installer" />Xamarin « Universal »

`~/Library/Logs/XamarinInstaller/Universal`

Il s’agit des journaux du programme d’installation de `XamarinInstaller.dmg`.

#### <a name="a-idmac-build-host-namemac-build-host-xamarin-build-host"></a><a id="mac-build-host" name="mac-build-host" />l’hôte de build Xamarin

`~/Library/Logs/Xamarin-[MAJOR.MINOR]`

## <a name="a-idverbose-build-output-logs-nameverbose-build-output-logs-verbose-build-output"></a><a id="verbose-build-output-logs" name="verbose-build-output-logs" />la sortie de la génération détaillée

1. Activez le diagnostic de la [sortie MSBuild](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output).

2. Pour les applications iOS, activez également la **sortie verbose mTouch** en ajoutant `-v -v -v -v` sous **propriétés du projet > Build iOS > général (onglet) > options supplémentaires > arguments mTouch supplémentaires**.

3. Nettoyez et régénérez le projet.

4. Copiez et collez la sortie de génération de l’IDE dans un fichier texte.
     - Visual Studio (Windows) : **afficher > sortie > afficher la sortie à partir de : Build**
     - Visual Studio pour Mac : **afficher les > pad > les erreurs > sortie de génération (onglet)**

## <a name="a-iddebug-logs-for-xamarin-apps-namedebug-logs-for-xamarin-apps-debug-logs-for-xamarinandroid-and-xamarinios-apps"></a><a id="debug-logs-for-xamarin-apps" name="debug-logs-for-xamarin-apps" />les journaux de débogage pour les applications Xamarin. Android et Xamarin. iOS

### <a name="visual-studio-for-mac"></a>Visual Studio pour Mac

**Afficher > Pad > sortie de l’application**

(Notez que cet élément de menu n’apparaît que lorsque l’application a été lancée.)

### <a name="visual-studio"></a>Visual Studio

**Afficher > sortie > Afficher la sortie à partir de : débogage**

### <a name="a-idadb-logcat-nameadb-logcat-android-adb-logcat-logs"></a><a id="adb-logcat" name="adb-logcat" />les journaux Android [`adb`](https://developer.android.com/tools/help/adb.html) logcat

Après avoir exécuté la commande `adb`, attachez à nouveau le fichier **android_logcat. txt** à partir de votre bureau. Ces instructions supposent que vous n’avez qu’un seul appareil attaché.

Consultez également la page [Journal de débogage Android](~/android/deploy-test/debugging/android-debug-log.md) .

#### <a name="visual-studio"></a>Visual Studio

1. **Outils > Android > Démarrer l’invite de commandes Android ADB**
2. Nettoyer le journal : `adb logcat -c`
3. Reproduisez le problème.
4. Sortie du journal : `adb logcat -vtime -d > "%USERPROFILE%\Desktop\android_logcat.txt"`

#### <a name="visual-studio-for-mac"></a>Visual Studio pour Mac

1. **Outils > Ouvrir Android SDK invite de commandes**
2. Nettoyer le journal : `adb logcat -c`
3. Reproduisez le problème.
4. Sortie du journal : `adb logcat -vtime -d > ~/Desktop/android_logcat.txt`

### <a name="a-idios-simulator-logs-nameios-simulator-logs-ios-simulator-logs-on-mac"></a><a id="ios-simulator-logs" name="ios-simulator-logs" />les journaux du simulateur iOS (sur Mac)

- Pour accéder au journal système, sélectionnez **Déboguer > ouvrir le journal système** dans l’application iOS Simulator.

- Pour afficher les rapports d’incidents à partir du simulateur, ouvrez console. app et accédez à `~/Library/Logs > DiagnosticReports`.

### <a name="a-idios-device-logs-nameios-device-logs-ios-device-logs-on-mac"></a><a id="ios-device-logs" name="ios-device-logs" />les journaux des appareils iOS (sur Mac)

#### <a name="visual-studio-for-mac"></a>Visual Studio pour Mac

**Afficher > Pad > Journal de l’appareil iOS**

#### <a name="xcode"></a>Xcode

**Appareils Windows > > $ {DeviceName}**

Les rapports d’incidents sont disponibles sous le bouton **afficher les journaux** de l’appareil. Le journal système de l’appareil s’affiche au bas de la fenêtre sous la flèche de diffusion.

#### <a name="xcode-5"></a>Xcode 5

**Windows > Organizer > appareils (onglet) > $ {DeviceName}**
