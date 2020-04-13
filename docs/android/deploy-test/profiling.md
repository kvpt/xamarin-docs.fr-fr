---
title: Profilage des applications Android
description: Ce guide explique comment utiliser les outils de profilage pour examiner les performances et l’utilisation de la mémoire d’une application Android.
ms.topic: article
ms.prod: xamarin
ms.assetid: 8C823FEE-A6F6-4C31-9EB6-E51407A2FD8E
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/03/2018
ms.openlocfilehash: fbd9fff6e7b63e86f0bba38f22025c5ea288f7c2
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027989"
---
# <a name="profiling-android-apps"></a>Profilage des applications Android

Avant de déployer votre application dans un magasin d’applications, il est important d’identifier et de résoudre les goulots d’étranglement de performances et les problèmes d’utilisation excessive de la mémoire ou d’utilisation inefficace des ressources réseau. Deux outils de profilage sont disponibles pour cela :

- Xamarin Profiler 
- Android Profiler dans Android Studio

Ce guide présente Xamarin Profiler et fournit des informations détaillées pour bien démarrer avec Android Profiler.

## <a name="xamarin-profiler"></a>Xamarin Profiler

Xamarin Profiler est une application autonome intégrée à Visual Studio et à Visual Studio pour Mac pour le profilage des applications Xamarin à partir de l’IDE. Pour plus d’informations sur l’utilisation de Xamarin Profiler, consultez [Xamarin Profiler](~/tools/profiler/index.md).

> [!NOTE]
> Vous devez être un abonné [Visual Studio Enterprise](https://visualstudio.microsoft.com/vs/compare/) pour pouvoir déverrouiller la fonctionnalité Xamarin Profiler dans Visual Studio Enterprise sur Windows ou Visual Studio pour Mac.

## <a name="android-studio-profiler"></a>Android Studio Profiler

Android Studio 3.0 et versions ultérieures incluent un outil nommé Android Profiler. Vous pouvez utiliser Android Profiler pour mesurer les performances d’une application Xamarin Android créée avec Visual Studio, sans avoir besoin d’une licence Visual Studio Enterprise. Toutefois, contrairement à Xamarin Profiler, Android Profiler n’est pas intégré à Visual Studio et peut être utilisé uniquement pour profiler un package d’application Android (APK) qui a été créé à l’avance et importé dans Android Profiler.

### <a name="launching-a-xamarin-android-app-in-android-profiler"></a>Lancement d’une application Xamarin Android dans Android Profiler

Les étapes suivantes expliquent comment lancer une application Xamarin Android dans l’outil Android Profiler d’Android Studio. Dans les exemples de captures d’écran ci-dessous, l’application Xamarin Forms [XamagonXuzzle](https://docs.microsoft.com/samples/xamarin/mobile-samples/liveplayer-xamagonxuzzlelp/) est générée et profilée à l’aide d’Android Profiler :

1. Dans les options de génération de projet Android, désactivez **Utiliser le runtime partagé**. Cela garantit que le package APK est généré sans dépendance envers le runtime Mono partagé au moment du développement.

    ![Désactivation de l’option Utiliser le runtime partagé](profiling-images/vswin/01-turn-off-shared-runtime.png)

2. Générez l’application pour **Debug** et déployez-la sur un appareil physique ou un émulateur. Cela provoque la génération d’une version **Debug** signée du package APK.
    Pour l’exemple **XamagonXuzzle**, le package APK résultant est appelé **com.companyname.XamagonXuzzle-Signed.apk**.

3. Ouvrez le dossier du projet et accédez à **bin/Debug**. Dans ce dossier, recherchez la version **Signed.apk** de l’application et copiez-la à un emplacement facilement accessible (par exemple le Bureau). Dans la capture d’écran suivante, le package APK **com.companyname.XamagonXuzzle-Signed.apk** est situé et copié sur le Bureau :

    [![Emplacement du fichier APK signé de débogé](profiling-images/vswin/02-locating-the-debug-apk-sml.png)](profiling-images/vswin/02-locating-the-debug-apk.png#lightbox)

4. Lancez Android Studio et sélectionnez **Profile or debug APK** :

    ![Démarrage du profileur à partir de l’écran de lancement d’Android Studio](profiling-images/vswin/03-android-studio.png)

5. Dans la boîte de dialogue **Select APK File**, naviguez jusqu’au package APK que vous avez créé et copié précédemment. Sélectionnez le package APK et cliquez sur **OK** : 
    
    ![Sélection du package APK dans la boîte de dialogue Select APK File](profiling-images/vswin/04-select-apk-dialog.png)

6. Android Studio charge le package APK et désassemble **classes.dex** :

    ![Configuration du package APK](profiling-images/vswin/05-setting-up-the-apk.png)

7. Une fois le package APK chargé, Android Studio affiche l’écran de projet suivant. Cliquez avec le bouton droit sur le nom de l’application dans l’arborescence à gauche et sélectionnez **Open Module Settings** :

    [![Emplacement de l’élément du menu Paramètres du module ouvert](profiling-images/vswin/06-open-module-settings-sml.png)](profiling-images/vswin/06-open-module-settings.png#lightbox)

8. Accédez à **Project Settings > Modules**, sélectionnez le nœud **-Signed** de l’application, puis cliquez sur **&lt;No SDK&gt;**  :

    [![Navigation vers le réglage SDK](profiling-images/vswin/07-project-settings-modules-sml.png)](profiling-images/vswin/07-project-settings-modules.png#lightbox)

9. Dans le menu déroulant **Module SDK**, sélectionnez le niveau de kit Android SDK qui a été utilisé pour générer l’application (dans cet exemple, le niveau d’API 26 a été utilisé pour générer **XamagonXuzzle**) :

    [![Définir le niveau SDK du projet](profiling-images/vswin/08-project-sdk-level-sml.png)](profiling-images/vswin/08-project-sdk-level.png#lightbox)

    Cliquez sur **Apply** et **OK** pour enregistrer ce paramètre.

10. Lancez le profileur à partir de l’icône de la barre d’outils :

    [![Emplacement de l’icône de la barre d’outils de profileur](profiling-images/vswin/09-launch-profiler-sml.png)](profiling-images/vswin/09-launch-profiler.png#lightbox)

11. Sélectionnez la cible de déploiement pour l’exécution/le profilage de l’application et cliquez sur **OK**. La cible de déploiement peut être un appareil physique ou un appareil virtuel en cours d’exécution dans un émulateur. Dans cet exemple, un appareil Nexus 5X est utilisé :

    ![Sélection de la cible de déploiement](profiling-images/vswin/10-select-deployment-target.png)

12. Quelques secondes après son démarrage, le profileur se connecte à l’appareil de déploiement et au processus d’application. Pendant l’installation du package APK, Android Profiler affichera les messages **No connected devices** et **No debuggable processes**.

    [![Le profileur installe l’APK](profiling-images/vswin/11-no-connected-devices-sml.png)](profiling-images/vswin/11-no-connected-devices.png#lightbox)

13. Après quelques secondes, Android Profiler termine l’installation du package APK et le lance. Il signale le nom de l’appareil et le nom du processus d’application en cours de profilage (dans cet exemple, **LGE Nexus 5X** et ** com.companyname.XamagonXuzzle**, respectivement) :

    [![Fenêtre de profileur après le début](profiling-images/vswin/12-profiler-starts-sml.png)](profiling-images/vswin/12-profiler-starts.png#lightbox)

14. Une fois l’appareil et le processus pouvant être débogué identifiés, Android Profiler commence le profilage de l’application :

    [![Affichages Profiler pour l’application en cours d’exécution](profiling-images/vswin/13-profiler-running-sml.png)](profiling-images/vswin/13-profiler-running.png#lightbox)

15. Si vous appuyez sur le bouton **RANDOMIZE** sur **XamagonXuzzle** (ce qui provoque le déplacement aléatoire des vignettes), vous observerez une augmentation de l’utilisation du processeur pendant l’intervalle de randomisation de l’application :

    [![Utilisation de CPU lorsque le bouton RANDOMIZE est exploité](profiling-images/vswin/14-tap-randomize-sml.png)](profiling-images/vswin/14-tap-randomize.png#lightbox)

### <a name="using-the-android-profiler"></a>Utilisation d’Android Profiler

Des informations détaillées sur l’utilisation d’Android Profiler sont fournies dans la [documentation d’Android Studio](https://developer.android.com/studio/profile/android-profiler.html).
Les rubriques suivantes pourront intéresser les développeurs Xamarin Android :

- [CPU Profiler](https://developer.android.com/studio/profile/cpu-profiler.html) &ndash; Explique comment inspecter l’activité des threads et l’utilisation du processeur par l’application en temps réel.

- [Memory Profiler](https://developer.android.com/studio/profile/memory-profiler.html) &ndash; Affiche un graphe en temps réel de l’utilisation de la mémoire par l’application, et inclut un bouton permettant d’enregistrer les allocations de mémoire à des fins d’analyse.

- [Network Profiler](https://developer.android.com/studio/profile/network-profiler.html) &ndash; Affiche l’activité du réseau (données envoyés et reçues par l’application) en temps réel.
