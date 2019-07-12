---
title: Où trouver les informations sur ma version et les journaux ?
description: Ce document décrit où rechercher pour rechercher des journaux et des informations de version de Xamarin. Ces informations sont utiles pour diagnostiquer les problèmes, signalant les bogues ou une prise en charge.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: CF386485-EAB0-4B9E-AA17-CB1B6462E505
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: e389bc33538ec3c3d36eb749c746f5a4723aab3c
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67830352"
---
# <a name="where-can-i-find-my-version-information-and-logs"></a>Où trouver les informations sur ma version et les journaux ?

## <a name="outline"></a>Contour

- [Informations de version](#version-information)
    - Informations de version de Windows
    - Informations sur la version Mac
    - Android SDK Tools, outils de plateforme, des outils de génération
- [Journaux IDE et le programme d’installation](#ide-and-installer-logs)
    - [Journaux Windows](#windows-logs)
        - Xamarin Studio
        - Xamarin pour Visual Studio
        - Programme d’installation de Xamarin Universal
        - Individuels `.msi` programmes d’installation, les journaux détaillés
        - Démarrage de Visual Studio, les journaux détaillés
    - [Journaux du Mac](#mac-logs)
        - Hôte de build
    - Visual Studio pour Mac
        - Xamarin Studio
        - Programme d’installation de Xamarin
- [Sortie de génération détaillée](#verbose-build-output-logs)
- [Déboguer les journaux pour les applications Xamarin.Android et Xamarin.iOS](#debug-logs-for-xamarin-apps)
    - Android `adb` logcat journaux
    - Simulateur iOS se connecte (Mac)
    - APPAREIL iOS se connecte (Mac)

## <a name="a-idversion-information-nameversion-information-version-information"></a><a id="version-information" name="version-information" />Informations de version

Il s’agit généralement mieux pour envoyer sauvegarde toutes les informations à partir de la **copier les informations** boutons. Sinon, nous devrons souvent demander des informations supplémentaires. Par exemple, versions de système d’exploitation, version de Xcode, installé des niveaux d’API Android, et version de .NET peut tous être importante pour aider à résoudre un problème.

### <a name="a-idwindows-version-information-namewindows-version-information-windows-version-information"></a><a id="windows-version-information" name="windows-version-information" />Informations de version de Windows

#### <a name="xamarin-studio"></a>Xamarin Studio

**Aide > à propos > Afficher les détails > Copier les informations [bouton]**

#### <a name="visual-studio"></a>Visual Studio

**Aide > à propos de Microsoft Visual Studio > Copier les informations [bouton]**

### <a name="a-idmac-version-information-namemac-version-information-mac-version-information"></a><a id="mac-version-information" name="mac-version-information" />Informations sur la version Mac

#### <a name="visual-studio-for-mac"></a>Visual Studio pour Mac

**Visual Studio > à propos de Visual Studio > Afficher les détails > Copier les informations [bouton]**

### <a name="a-idandroid-sdk-tools-versions-nameandroid-sdk-tools-versions-android-sdk-tools-platform-tools-build-tools"></a><a id="android-sdk-tools-versions" name="android-sdk-tools-versions" />Android SDK Tools, outils de plateforme, des outils de génération

Ouvrez le gestionnaire Android SDK et prendre une capture d’écran du haut **outils** section.

#### <a name="visual-studio-for-mac"></a>Visual Studio pour Mac

**Outils > Ouvrir Android SDK Manager**

#### <a name="visual-studio"></a>Visual Studio

**Outils > Android > Ouvrir Android SDK Manager...**

## <a name="a-idide-and-installer-logs-nameide-and-installer-logs-ide-and-installer-logs"></a><a id="ide-and-installer-logs" name="ide-and-installer-logs" />Journaux IDE et le programme d’installation

Pour chaque emplacement du journal, veillez à compresser et attacher le dossier de la totalité du journal.

### <a name="a-idwindows-logs-namewindows-logs-windows-logs"></a><a id="windows-logs" name="windows-logs" />Journaux Windows

#### <a name="a-idwindows-logs-xamarin-vs-namewindows-logs-xamarin-vs--visual-studio-tools-for-xamarin"></a><a id="windows-logs-xamarin-vs" name="windows-logs-xamarin-vs" /> Visual Studio Tools pour Xamarin

`%LOCALAPPDATA%\Xamarin\Logs`

#### <a name="a-idvs-2017-namevs-2017--visual-studio-2017"></a><a id="vs-2017" name="vs-2017" /> Visual Studio 2017

[Comment obtenir les journaux d’installation de Visual Studio](https://docs.microsoft.com/visualstudio/install/troubleshooting-installation-issues#how-to-get-the-visual-studio-installation-logs)

#### <a name="a-idvs-2015-namevs-2015--visual-studio-2015"></a><a id="vs-2015" name="vs-2015" /> Visual Studio 2015

#### <a name="a-idwindows-universal-installer-namewindows-universal-installer--xamarin-universal-installer"></a><a id="windows-universal-installer" name="windows-universal-installer" /> Programme d’installation « Universelles » Xamarin

`%LOCALAPPDATA%\Xamarin\Universal`

Voici les journaux à partir de la `XamarinInstaller.exe` programme d’installation.

#### <a name="a-idindividual-msi-installers-verbose-logs-nameindividual-msi-installers-verbose-logs-individual-msi-installers-verbose-logs"></a><a id="individual-msi-installers-verbose-logs" name="individual-msi-installers-verbose-logs" />Individuels `.msi` programmes d’installation, les journaux détaillés

```csharp
msiexec /i Xamarin.msi /l*vx "%USERPROFILE%\Desktop\Xamarin.log"
```

Référence : [Options de ligne de commande](https://msdn.microsoft.com/library/aa367988.aspx)

#### <a name="a-idvisual-studio-startup-verbose-logs-namevisual-studio-startup-verbose-logs-visual-studio-startup-verbose-logs"></a><a id="visual-studio-startup-verbose-logs" name="visual-studio-startup-verbose-logs" />Démarrage de Visual Studio, les journaux détaillés

```csharp
devenv.exe /log "%USERPROFILE%\Desktop\VisualStudio.log"
```

Référence : [/Log (devenv.exe)](https://msdn.microsoft.com/library/ms241272.aspx)

### <a name="a-idmac-logs-namemac-logs-mac-logs"></a><a id="mac-logs" name="mac-logs" />Journaux du Mac

Vous pouvez sélectionner le **accédez > accédez au dossier** menu d’élément dans le Finder, puis copiez et collez un de ces chemins d’accès dans la boîte de dialogue.

#### <a name="a-idmac-logs-visual-studio-namemac-logs-visual-studio-visual-studio-for-mac"></a><a id="mac-logs-visual-studio" name="mac-logs-visual-studio" />Visual Studio pour Mac

`~/Library/Logs/VisualStudio/7.0` (ce nombre peut changer selon la version que vous utilisez)

Ce dossier peut également être ouvert par le biais de « Aide -> Ouvrir le répertoire Log ».

#### <a name="a-idmac-logs-xamarin-studio-namemac-logs-xamarin-studio-xamarin-studio"></a><a id="mac-logs-xamarin-studio" name="mac-logs-xamarin-studio" />Xamarin Studio

`~/Library/Logs/XamarinStudio-6.0` (ce nombre peut changer selon la version que vous utilisez)

Ce dossier peut également être ouvert par le biais de « Aide -> Ouvrir le répertoire Log ».

#### <a name="a-idmac-universal-installer-namemac-universal-installer-xamarin-universal-installer"></a><a id="mac-universal-installer" name="mac-universal-installer" />Programme d’installation « Universelles » Xamarin

`~/Library/Logs/XamarinInstaller/Universal`

Voici les journaux à partir de la `XamarinInstaller.dmg` programme d’installation.

#### <a name="a-idmac-build-host-namemac-build-host-xamarin-build-host"></a><a id="mac-build-host" name="mac-build-host" />Hôte de Build Xamarin

`~/Library/Logs/Xamarin-[MAJOR.MINOR]`

## <a name="a-idverbose-build-output-logs-nameverbose-build-output-logs-verbose-build-output"></a><a id="verbose-build-output-logs" name="verbose-build-output-logs" />Sortie de génération détaillée

1.  Activer [sortie MSBuild de diagnostic](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output).

2.  Pour les applications iOS, activer également **mtouch verbose sortie** en ajoutant `-v -v -v -v` sous **propriétés du projet > Build iOS > Général (onglet) > Options supplémentaires > arguments mtouch supplémentaires**.

3.  Nettoyez et regénérez le projet.

4.  Copiez et collez la sortie de génération à partir de l’IDE dans un fichier texte.
     - Visual Studio (Windows) : **Affichage > sortie > Afficher la sortie à partir de : Build**
     - Visual Studio pour Mac : **Afficher > panneaux > erreurs > sortie de la Build (onglet)**

## <a name="a-iddebug-logs-for-xamarin-apps-namedebug-logs-for-xamarin-apps-debug-logs-for-xamarinandroid-and-xamarinios-apps"></a><a id="debug-logs-for-xamarin-apps" name="debug-logs-for-xamarin-apps" />Déboguer les journaux pour les applications Xamarin.Android et Xamarin.iOS

### <a name="visual-studio-for-mac"></a>Visual Studio pour Mac

**Afficher > panneaux > sortie de l’Application**

(Notez que cet élément de menu apparaît uniquement une fois que l’application a été lancée.)

### <a name="visual-studio"></a>Visual Studio

**Affichage > sortie > Afficher la sortie à partir de : Débogage**

### <a name="a-idadb-logcat-nameadb-logcat-android-adbhttpsdeveloperandroidcomtoolshelpadbhtml-logcat-logs"></a><a id="adb-logcat" name="adb-logcat" />Android [ `adb` ](https://developer.android.com/tools/help/adb.html) logcat journaux

Après l’exécution du `adb` de commande, rattachez-les le **android_logcat.txt** fichier à partir de votre bureau. Ces instructions partent du principe qu’un seul appareil attaché.

Voir aussi le [journal de débogage Android](~/android/deploy-test/debugging/android-debug-log.md) page.

#### <a name="visual-studio"></a>Visual Studio

1. **Outils > Android > Démarrer l’invite de commandes Android Adb**
2. Nettoyer le journal : `adb logcat -c`
3. Reproduisez le problème.
4. Le journal de sortie : `adb logcat -vtime -d > "%USERPROFILE%\Desktop\android_logcat.txt"`

#### <a name="visual-studio-for-mac"></a>Visual Studio pour Mac

1. **Outils > Ouvrir l’invite de commandes du Kit Android SDK**
2. Nettoyer le journal : `adb logcat -c`
3. Reproduisez le problème.
4. Le journal de sortie : `adb logcat -vtime -d > ~/Desktop/android_logcat.txt`

### <a name="a-idios-simulator-logs-nameios-simulator-logs-ios-simulator-logs-on-mac"></a><a id="ios-simulator-logs" name="ios-simulator-logs" />Simulateur iOS se connecte (Mac)

* Pour accéder au journal système, sélectionnez **Déboguer > Ouvrir le journal système...**  dans l’application de simulateur iOS.

* Pour afficher les rapports d’incidents à partir du simulateur, ouvrez Console.app et accédez à `~/Library/Logs > DiagnosticReports`.

### <a name="a-idios-device-logs-nameios-device-logs-ios-device-logs-on-mac"></a><a id="ios-device-logs" name="ios-device-logs" />APPAREIL iOS se connecte (Mac)

#### <a name="visual-studio-for-mac"></a>Visual Studio pour Mac

**Affichage > blocs > journal de l’appareil d’iOS**

#### <a name="xcode"></a>Xcode

**Fenêtre > appareils > ${DeviceName}**

Rapports d’incident sont disponibles sous le **afficher les journaux de périphérique** bouton. Le journal système pour l’appareil s’affiche en bas de la fenêtre sous la flèche commandant l’affichage.

#### <a name="xcode-5"></a>Xcode 5

**Fenêtre > organisateur > appareils (onglet) > ${DeviceName}**
