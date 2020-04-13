---
title: Conseils de dépannage
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 56137ACA-4811-B312-6860-E16D0FA123F7
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/15/2018
ms.openlocfilehash: 6d83afa47c459633506736b2497a82c444352c90
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "79303917"
---
# <a name="troubleshooting-tips"></a>Conseils de dépannage

## <a name="getting-diagnostic-information"></a>Obtenir des informations diagnostiques

Xamarin.Android a quelques endroits à regarder lors de la traque de divers bugs.
notamment :

1. Sortie diagnostique MSBuild.
2. Journaux de déploiement de l’appareil.
3. Android Debug Log Sortie.

<a name="Diagnostic_MSBuild_Output" />

## <a name="diagnostic-msbuild-output"></a>Sortie diagnostique MSBuild

Le MSBuild diagnostique peut contenir des informations supplémentaires relatives à la construction de colis et peut contenir des informations sur le déploiement de colis.

Pour activer la sortie MSBuild de diagnostic dans Visual Studio :

1. Cliquez sur **Outils > Options...**
2. Dans la vue de l’arbre de gauche, sélectionnez **projets et solutions > Construire et exécuter**
3. Dans le panneau de droite, définissez le msBuild construire la verbosité de sortie à Diagnostic
4. Cliquez sur **OK**
5. Nettoyez et regénérez votre package.
6. La sortie diagnostique est visible dans le panneau de sortie.

Pour activer la sortie diagnostique MSBuild au sein de Visual Studio pour Mac/OS X :

1. Cliquez **sur Visual Studio pour Mac > Préférences...**
2. Dans la vue de l’arbre de gauche, sélectionnez **les projets > Construire**
3. Dans le panneau de droite, définissez le dépôt de verbosité de log à Diagnostic
4. Cliquez sur **OK**
5. Redémarrer Visual Studio pour Mac
6. Nettoyez et regénérez votre package.
7. La sortie diagnostique est visible dans le Pad d’erreurs **(Voir > Pads > Erreurs** ), en cliquant sur le bouton Build Output.

## <a name="device-deployment-logs"></a>Journaux de déploiement de l’appareil

Pour activer l’enregistrement du déploiement de l’appareil au sein de Visual Studio :

1. **Outils > Options...**>
2. Dans la vue de l’arbre de gauche, sélectionnez **Xamarin > Paramètres Android**
3. Dans le panneau de droite, activez la **case à cocher [X] extension debug (écrit monodroid.log sur votre bureau).**
4. Les messages journalaux sont écrits au fichier monodroid.log sur votre bureau.

Visual Studio pour Mac écrit toujours des journaux de déploiement d’appareils. Il est un peu plus difficile de les faire en difficulté; un fichier de journal *AndroidUtils* est créé pour tous les jours - le temps qu’un déploiement se produit, par exemple: **AndroidTools-2012-10-24-12-35-45.log**.

- Sur Windows, les fichiers `%LOCALAPPDATA%\XamarinStudio-{VERSION}\Logs`journaux sont écrits à .
- Sur OS X, les `$HOME/Library/Logs/XamarinStudio-{VERSION}`fichiers journaux sont écrits à .

## <a name="android-debug-log-output"></a>Sortie Android Debug Log

Android va écrire de nombreux messages à [l’Android Debug Log](~/android/deploy-test/debugging/android-debug-log.md).
Xamarin.Android utilise les propriétés du système Android pour contrôler la génération de messages supplémentaires à l’Android Debug Log. Les propriétés du système Android peuvent être définies via la commande *setprop* dans le [pont Android Debug (adb)](https://developer.android.com/guide/developing/tools/adb.html):

```shell
adb shell setprop PROPERTY_NAME PROPERTY_VALUE
```

Les propriétés du système sont lues pendant le démarrage du processus, et doivent donc être réglées avant le lancement de l’application, soit l’application doit être redémarrée après que les propriétés du système ont été modifiées.

### <a name="xamarinandroid-system-properties"></a>Propriétés système de Xamarin.Android

Xamarin.Android prend en charge les propriétés système suivantes :

- *debug.mono.debug*: Si une chaîne non vide, c’est l’équivalent de `*mono-debug*`.

- *debug.mono.env*: Une liste*|* de variables de l’environnement séparée par les tuyaux () à exporter pendant le démarrage de l’application, *avant* que le mono n’ait été paraségé. Cela permet de définir des variables de l’environnement qui contrôlent l’enregistrement mono.

  > [!NOTE]
  > Étant donné que*|* la valeur est «-séparée, la valeur doit \`avoir un niveau supplémentaire de citation, comme la commande *de coquilles* \` de bad supprimera un ensemble de devis.

  > [!NOTE]
  > Les valeurs des propriétés du système Android ne peuvent pas dépasser 92 caractères.

  Exemple :

  ```
  adb shell setprop debug.mono.env "'MONO_LOG_LEVEL=info|MONO_LOG_MASK=asm'"
  ```

- *debug.mono.log*: Une liste de composants séparés par virgule ('*,*') qui devrait imprimer des messages supplémentaires à l’Android Debug Log. Par défaut, rien n’est réglé. Les composants comprennent :

  - *tous*: Imprimez tous les messages
  - *gc*: Imprimez des messages liés à la GC.
  - *gref*: Imprimer (faible, global) des messages d’attribution de référence et de deallocation.
  - *lref*: Imprimez des messages locaux d’attribution de référence et de deallocation.

  > [!NOTE]
  > Ce sont *extrêmement* verbeux. Ne pas activer à moins que vous avez vraiment besoin.

- *debug.mono.trace*: Permet de définir le [réglage mono --trace.](http://docs.go-mono.com/?link=man%3amono(1)) `=PROPERTY_VALUE`

## <a name="deleting-bin-and-obj"></a>Suppression `bin` et`obj`

Xamarin.Android a souffert dans le passé d’une situation telle que:

- Vous rencontrez une étrange erreur de construction ou de temps d’exécution.
- Vous `Clean` `Rebuild`, , ou `bin` supprimer `obj` manuellement votre répertoire et.
- Le problème disparaît.

Nous sommes fortement investis dans la résolution de problèmes comme ceux-ci en raison de leur impact sur la productivité des développeurs.

Si un tel problème vous arrive :

1. Prenez une note mentale. Quelle a été la dernière action qui a permis à votre projet d’entrer dans cet état?
1. Enregistrez votre journal de construction actuel. Essayez de construire à nouveau, et enregistrer un [journal de construction de diagnostic](#diagnostic-msbuild-output).
1. Soumettez un [rapport de bogue][bug].

Avant de supprimer `bin` `obj` vos répertoires et vos répertoires, entez-les et économisez-les pour un diagnostic ultérieur si nécessaire. Vous pouvez `Clean` probablement simplement votre projet d’application Xamarin.Android pour faire fonctionner les choses à nouveau.

[bug]: https://github.com/xamarin/xamarin-android/wiki/Submitting-Bugs,-Feature-Requests,-and-Pull-Requests

## <a name="xamarinandroid-cannot-resolve-systemvaluetuple"></a>Xamarin.Android ne peut pas résoudre System.ValueTuple

Cette erreur se produit en raison d’une incompatibilité avec Visual Studio.

- **Visual Studio 2017 Mise à jour 1** (version 15.1 ou plus) n’est compatible qu’avec le **System.ValueTuple NuGet 4.3.0** (ou plus).

- **Visual Studio 2017 Mise à jour 2** (version 15.2 ou plus récente) n’est compatible qu’avec le **System.ValueTuple NuGet 4.3.1** (ou plus récent).

S’il vous plaît choisir le bon System.ValueTuple NuGet qui correspond à votre installation Visual Studio 2017.

## <a name="gc-messages"></a>GC Messages

Les messages de composants GC peuvent être consultés en définissant la propriété du système debug.mono.log à une valeur qui contient gc.

Les messages GC sont générés chaque fois que le GC exécute et fournit des informations sur la quantité de travail que le GC a fait :

```shell
I/monodroid-gc(12331): GC cleanup summary: 81 objects tested - resurrecting 21.
```

Des informations supplémentaires sur le GC, `MONO_LOG_LEVEL` telles que `debug`l’information sur le moment, peuvent être générées en définissant la variable de l’environnement pour :

```shell
adb shell setprop debug.mono.env MONO_LOG_LEVEL=debug
```

Cela se traduira par (beaucoup) de messages Mono supplémentaires, y compris ces trois de conséquence:

```shell
D/Mono (15723): GC_BRIDGE num-objects 1 num_hash_entries 81226 sccs size 81223 init 0.00ms df1 285.36ms sort 38.56ms dfs2 50.04ms setup-cb 9.95ms free-data 106.54ms user-cb 20.12ms clenanup 0.05ms links 5523436/5523436/5523096/1 dfs passes 1104 6883/11046605
D/Mono (15723): GC_MINOR: (Nursery full) pause 2.01ms, total 287.45ms, bridge 225.60 promoted 0K major 325184K los 1816K
D/Mono ( 2073): GC_MAJOR: (user request) pause 2.17ms, total 2.47ms, bridge 28.77 major 576K/576K los 0K/16K
```

Dans `GC_BRIDGE` le `num-objects` message, est le nombre d’objets `num_hash_entries` de pont que ce passage envisage, et est le nombre d’objets traités au cours de cette invocation du code de pont.

Dans `GC_MINOR` le `GC_MAJOR` et `total` les messages, est la quantité de temps pendant que `bridge` le monde est en pause (aucun thread ne sont en cours d’exécution), tandis que c’est la quantité de temps pris dans le code de traitement du pont (qui traite avec le Java VM). Le monde *n’est pas* en pause pendant le traitement du pont.

 *En général,* plus la `num_hash_entries`valeur de , `bridge` plus les collections prendront `total` de temps, et plus le temps passé à la collecte sera grand.

## <a name="global-reference-messages"></a>Messages de référence globaux

Pour permettre l’exploitation forestière global de référence (GREF), la propriété du système *de debug.mono.log* doit contenir *du gref,* par exemple :

```shell
adb shell setprop debug.mono.log gref
```

Xamarin.Android utilise des références mondiales Android pour fournir des cartographies entre les instances Java et les instances gérées associées, comme lors de l’invocation d’une méthode Java une instance Java doit être fournie à Java.

Malheureusement, les émulateurs Android ne permettent que 2000 références mondiales d’exister à la fois. Le matériel a une limite beaucoup plus élevée de 52000 références mondiales. La limite inférieure peut être problématique lors de l’exécution des applications sur l’émulateur, donc savoir *d’où* vient l’instance peut être très utile.

> [!NOTE]
> Le décompte global des références est interne à Xamarin.Android, et ne comprend pas (et ne peut pas) inclure des références globales prises par d’autres bibliothèques autochtones chargées dans le processus. Utilisez le nombre de références globale comme estimation.

```shell
I/monodroid-gref(12405): +g+ grefc 108 gwrefc 0 obj-handle 0x40517468/L -> new-handle 0x40517468/L from    at Java.Lang.Object.RegisterInstance(IJavaObject instance, IntPtr value, JniHandleOwnership transfer)
I/monodroid-gref(12405):    at Java.Lang.Object.SetHandle(IntPtr value, JniHandleOwnership transfer)
I/monodroid-gref(12405):    at Java.Lang.Object..ctor(IntPtr handle, JniHandleOwnership transfer)
I/monodroid-gref(12405):    at Java.Lang.Thread+RunnableImplementor..ctor(System.Action handler, Boolean removable)
I/monodroid-gref(12405):    at Java.Lang.Thread+RunnableImplementor..ctor(System.Action handler)
I/monodroid-gref(12405):    at Android.App.Activity.RunOnUiThread(System.Action action)
I/monodroid-gref(12405):    at Mono.Samples.Hello.HelloActivity.UseLotsOfMemory(Android.Widget.TextView textview)
I/monodroid-gref(12405):    at Mono.Samples.Hello.HelloActivity.<OnCreate>m__3(System.Object o)
I/monodroid-gref(12405): handle 0x40517468; key_handle 0x40517468: Java Type: `mono/java/lang/RunnableImplementor`; MCW type: `Java.Lang.Thread+RunnableImplementor`
I/monodroid-gref(12405): Disposing handle 0x40517468
I/monodroid-gref(12405): -g- grefc 107 gwrefc 0 handle 0x40517468/L from    at Java.Lang.Object.Dispose(System.Object instance, IntPtr handle, IntPtr key_handle, JObjectRefType handle_type)
I/monodroid-gref(12405):    at Java.Lang.Object.Dispose()
I/monodroid-gref(12405):    at Java.Lang.Thread+RunnableImplementor.Run()
I/monodroid-gref(12405):    at Java.Lang.IRunnableInvoker.n_Run(IntPtr jnienv, IntPtr native__this)
I/monodroid-gref(12405):    at System.Object.c200fe6f-ac33-441b-a3a0-47659e3f6750(IntPtr , IntPtr )
I/monodroid-gref(27679): +w+ grefc 1916 gwrefc 296 obj-handle 0x406b2b98/G -> new-handle 0xde68f4bf/W from take_weak_global_ref_jni
I/monodroid-gref(27679): -w- grefc 1915 gwrefc 294 handle 0xde691aaf/W from take_global_ref_jni
```

Il y a quatre messages de conséquence :

- Création de référence globale : ce sont les lignes qui commencent par *'g',* et fourniront une trace de pile pour le chemin de code de création.
- Destruction de référence globale: ce sont les lignes qui commencent par *-g-* , et peut fournir une trace de pile pour le chemin de code se débarrassant de la référence globale. Si le GC se débarrasse du gref, aucune trace de pile ne sera fournie.
- Faible création de référence globale: ce sont les lignes qui commencent par *'w '.*
- Faible destruction de référence globale: ce sont des lignes qui commencent par *-w-* .

Dans tous les messages, la valeur *grefc* est le nombre de références globales que Xamarin.Android a créé, tandis que la valeur *grefwc* est le compte de faibles références mondiales que Xamarin.Android a créé. La *valeur de poignée* ou *d’obj-handle* est la */* valeur de poignée JNI, et le caractère après le ' est le type de valeur de manche: */L* pour la référence locale, */G* pour les références globales, et */W* pour les références mondiales faibles.

Dans le cadre du processus GC, les références globales sont converties en références mondiales faibles (causant un 'w' et -g-), un GC côté Java est botté, puis la faible référence globale est vérifiée pour voir si elle a été collectée. S’il est encore en vie, un nouveau gref est créé autour de l’arbitre faible (g, -w-), sinon l’arbitre faible est détruit (-w).

## <a name="java-instance-is-created-and-wrapped-by-a-mcw"></a>L’instance Java est créée et enveloppée par un MCW

```shell
I/monodroid-gref(27679): +g+ grefc 2211 gwrefc 0 obj-handle 0x4066df10/L -> new-handle 0x4066df10/L from ...
I/monodroid-gref(27679): handle 0x4066df10; key_handle 0x4066df10: Java Type: `android/graphics/drawable/TransitionDrawable`; MCW type: `Android.Graphics.Drawables.TransitionDrawable`
```

## <a name="a-gc-is-being-performed"></a>Un GC est en cours d’exécution...

```shell
I/monodroid-gref(27679): +w+ grefc 1953 gwrefc 259 obj-handle 0x4066df10/G -> new-handle 0xde68f95f/W from take_weak_global_ref_jni
I/monodroid-gref(27679): -g- grefc 1952 gwrefc 259 handle 0x4066df10/G from take_weak_global_ref_jni
```

## <a name="object-is-still-alive-as-handle--null"></a>L’objet est toujours vivant, comme poignée !
## <a name="wref-turned-back-into-a-gref"></a>wref se retourna en un gref

```shell
I/monodroid-gref(27679): *try_take_global obj=0x4976f080 -> wref=0xde68f95f handle=0x4066df10
I/monodroid-gref(27679): +g+ grefc 1930 gwrefc 39 obj-handle 0xde68f95f/W -> new-handle 0x4066df10/G from take_global_ref_jni
I/monodroid-gref(27679): -w- grefc 1930 gwrefc 38 handle 0xde68f95f/W from take_global_ref_jni
```

## <a name="object-is-dead-as-handle--null"></a>L’objet est mort, comme poignée nulle
## <a name="wref-is-freed-no-new-gref-created"></a>wref est libéré, pas de nouveau gref créé

```shell
I/monodroid-gref(27679): *try_take_global obj=0x4976f080 -> wref=0xde68f95f handle=0x0
I/monodroid-gref(27679): -w- grefc 1914 gwrefc 296 handle 0xde68f95f/W from take_global_ref_jni
```

Il ya une ride "intéressante" ici: sur les cibles en cours d’exécution Android avant 4.0, la valeur gref est égale à l’adresse de l’objet Java dans la mémoire du temps d’exécution Android. (C’est-à-dire que le GC est un non-mouvement, conservateur, collectionneur, et il distribue des références directes à ces objets.) Ainsi, après une séquence de gref résultante, après un «g», 'w', 'g', 'w', le gref résultant aura la même valeur que la valeur gref d’origine. Cela rend grepping à travers les journaux assez simple.

Android 4.0, cependant, a un collecteur en mouvement et ne distribue plus de références directes aux objets Android runtime VM. Par conséquent, après une séquence de gref, 'g', 'g', 'g', 'w', la valeur du gref *sera différente.* Si l’objet survit à plusieurs CPG, il ira par plusieurs valeurs gref, ce qui rend plus difficile de déterminer où une instance a été effectivement allouée à partir.

### <a name="querying-programmatically"></a>Requête programmatique

Vous pouvez interroger à la fois le GREF `JniRuntime` et WREF compte en interrogeant l’objet.

`Java.Interop.JniRuntime.CurrentRuntime.GlobalReferenceCount`- Nombre de références mondiales

`Java.Interop.JniRuntime.CurrentRuntime.WeakGlobalReferenceCount`- Faible décompte des références

## <a name="android-debug-logs"></a>Journaux Android Debug

Les [journaux Android Debug](~/android/deploy-test/debugging/android-debug-log.md) peuvent fournir un contexte supplémentaire en ce qui concerne toutes les erreurs de temps d’exécution que vous voyez.

## <a name="floating-point-performance-is-terrible"></a>La performance de Floating-Point est terrible !

Alternativement, "Mon application fonctionne 10x plus vite avec la construction Debug qu’avec la version Release"

Xamarin.Android prend en charge plusieurs appareils ABIs: *armeabi*, *armeabi-v7a*, et *x86*. Les ABI de l’appareil peuvent être spécifiés dans **l’onglet Propriétés de projet >'application > architectures soutenues.**

Debug construit utiliser un paquet Android qui fournit tous les ABI, et donc utiliser l’ABI le plus rapide pour l’appareil cible.

Les builds de version n’incluront que les ATI sélectionnées dans l’onglet Propriétés du projet. Plus d’un peut être sélectionné.

*armeabi* est l’ABI par défaut, et dispose du support appareil le plus large. *Cependant,* armeabi ne prend pas en charge les appareils multi-processeurs et le matériel flottant-point, amont d’autres choses. Par conséquent, les applications utilisant l’heure d’exécution armeabi Release seront liées à un seul noyau et utiliseront une implémentation soft-float. Ces deux éléments peuvent contribuer à des performances nettement plus lentes pour votre application.

Si votre application nécessite des performances flottantes décentes (par exemple les jeux), vous devez activer *l’armeabi-v7a* ABI. Vous pouvez ne prendre en charge que *l’heure d’exécution armeabi-v7a,* mais cela signifie que les appareils plus anciens qui ne prennent en charge *armeabi* ne sera pas en mesure d’exécuter votre application.

## <a name="could-not-locate-android-sdk"></a>Impossible de localiser Android SDK

Il ya 2 téléchargements disponibles sur Google pour l’Android SDK pour Windows.
Si vous choisissez l’installateur .exe, il écrira des clés de registre qui disent Xamarin.Android où il a été installé. Si vous choisissez le fichier .zip et le décompresser vous-même, Xamarin.Android ne sait pas où chercher le SDK. Vous pouvez dire Xamarin.Android où le SDK est en studio visuel en allant à **Tools > Options > Xamarin > Paramètres Android:**

[![Emplacement Android SDK dans Xamarin Paramètres Android](troubleshooting-images/01.png)](troubleshooting-images/01.png#lightbox)

## <a name="ide-does-not-display-target-device"></a>IDE n’affiche pas l’appareil cible

Parfois, vous tenterez de déployer votre application sur un appareil, mais l’appareil que vous souhaitez déployer n’est pas affiché dans le dialogue De l’appareil select. Cela peut arriver lorsque l’Android Debug Bridge décide de partir en vacances.

Pour diagnostiquer ce problème, trouver le [programme adb](~/android/deploy-test/debugging/android-debug-log.md), puis exécuter:

```shell
adb devices
```

Si votre appareil n’est pas présent, alors vous devez redémarrer le serveur Android Debug Bridge afin que votre appareil puisse être trouvé:

```shell
adb kill-server
adb start-server
```

Le logiciel HTC Sync peut empêcher **le serveur de démarrage** de bid de fonctionner correctement. Si la commande **de start-server** de bid n’imprime pas sur quel port il commence, s’il vous plaît quitter le logiciel HTC Sync et essayez de redémarrer le serveur de secours.

## <a name="the-specified-task-executable-keytool-could-not-be-run"></a>La tâche spécifiée exécutable "keytool" ne pouvait pas être exécuté

Cela signifie que votre PATH ne contient pas le répertoire où se trouve le répertoire des bacs java SDK. Vérifiez que vous avez suivi les étapes du guide [d’installation.](~/android/get-started/installation/index.md)

## <a name="monodroidexe-or-aresgenexe-exited-with-code-1"></a>monodroid.exe ou aresgen.exe est sorti avec le code 1

Pour vous aider à déboguer ce problème, allez dans Visual Studio et changer le niveau de verbosité MSBuild, pour ce faire, sélectionnez: **Outils > Options > Projet** et Solutions > **Construire** et exécuter **> MSBuild Project Build Output Verbosity** et définir cette valeur à **normal**.

Reconstruire, et vérifier la vitre de sortie de Visual Studio, qui doit contenir l’erreur complète.

## <a name="there-is-not-enough-storage-space-on-the-device-to-deploy-the-package"></a>Il n’y a pas assez d’espace de stockage sur l’appareil pour déployer le paquet

Cela se produit lorsque vous ne démarrez pas l’émulateur à partir de Visual Studio. Lorsque vous démarrez l’émulateur à l’extérieur de Visual Studio, vous devez passer les `-partition-size 512` options, par exemple.

```shell
emulator -partition-size 512 -avd MonoDroid
```

Assurez-vous d’utiliser le nom de simulateur correct, c’est-à-dire [le nom que vous avez utilisé lors de la configuration du simulateur](~/android/get-started/installation/windows.md#device).

## <a name="install_failed_invalid_apk-when-installing-a-package"></a>INSTALL\_\_FAILED\_INVALID APK lors de l’installation d’un paquet

Les noms de paquets Android *doivent* contenir une période ('*.*'). Modifiez votre nom de paquet afin qu’il contienne une période.

- Au sein de Visual Studio:
  - Cliquez à droite sur votre projet > Propriétés
  - Cliquez sur l’onglet Android Manifest sur la gauche.
  - Mettre à jour le champ de noms du paquet.
    - Si vous voyez &ldquo;le message No AndroidManifest.xml trouvé. Cliquez pour en ajouter un. &rdquo;, cliquez sur le lien, puis mettez à jour le champ de nom du paquet.
- Au sein de Visual Studio pour Mac:
  - Cliquez à droite sur votre projet > Options.
  - Naviguez vers la section Build / Android Application.
  - Modifier le champ de nom de paquet pour contenir un '.'.

## <a name="install_failed_missing_shared_library-when-installing-a-package"></a>INSTALL\_\_FAILED\_\_MISSING SHARED LIBRARY lors de l’installation d’un paquet

Une « bibliothèque partagée » dans ce contexte *n’est pas* un fichier de bibliothèque partagée autochtone *(libfoo.so)*; il s’agit plutôt d’une bibliothèque qui doit être installée séparément sur l’appareil cible, comme Google Maps.

Le paquet Android spécifie quelles `<uses-library/>` bibliothèques partagées sont nécessaires avec l’élément. Si une bibliothèque *requise* n’est pas présente `//uses-library/@android:required` sur l’appareil cible (par exemple, c’est *vrai,* ce qui est par défaut), l’installation du paquet échouera avec *\_INSTALL\_FAILED MISSING\_SHARED\_LIBRARY*.

Pour déterminer quelles bibliothèques partagées sont requises, consultez le fichier**AndroidManifest.xml** *généré*
(par exemple **obj\\Debug\\android\\AndroidManifest.xml**) et rechercher les `<uses-library/>` éléments. `<uses-library/>`les éléments peuvent être ajoutés manuellement dans le fichier **Propriétés\\AndroidManifest.xml** de votre projet et via l’attribut [personnalisé UsesLibraryAttribute](xref:Android.App.UsesLibraryAttribute).

Par exemple, l’ajout d’une référence d’assemblage à *Mono.Android.GoogleMaps.dll* ajoutera implicitement un `<uses-library/>` pour la bibliothèque partagée Google Maps.

## <a name="install_failed_update_incompatible-when-installing-a-package"></a>INSTALL\_\_FAILED\_UPDATE INCOMPATIBLE lors de l’installation d’un paquet

Les forfaits Android ont trois exigences :

- Ils doivent contenir un '.' (voir l’entrée précédente)
- Ils doivent avoir un nom de paquet de chaîne unique (d’où la convention inverse-tld vu dans les noms d’applications Android, par exemple com.android.chrome pour l’application Chrome)
- Lors de la mise à niveau des paquets, le paquet doit avoir la même clé de signature.

Imaginez donc ce scénario :

1. Vous construisez & déployer votre application sous forme d’application Debug
2. Vous modifiez la clé de signature, par exemple pour l’utiliser comme application De Release (ou parce que vous n’aimez pas la clé de signature Debug fournie par défaut)
3. Vous installez votre application sans la supprimer d’abord, par exemple Debug > Démarrer sans débogage au sein de Visual Studio

Lorsque cela se produit, l’installation\_\_du paquet échouera avec une erreur INSTALL\_FAILED UPDATE INCOMPATIBLE, parce que le nom du paquet n’a pas changé pendant que la clé de signature a fait. [L’Android Debug Log](~/android/deploy-test/debugging/android-debug-log.md) contiendra également un message similaire à :

```shell
E/PackageManager(  146): Package [PackageName] signatures do not match the previously installed version; ignoring!
```

Pour corriger cette erreur, supprimez complètement l’application de votre appareil avant de réin installation.

## <a name="install_failed_uid_changed-when-installing-a-package"></a>INSTALL\_\_FAILED\_UID CHANGED lors de l’installation d’un paquet

Lorsqu’un paquet Android est installé, il est attribué un *identifiant utilisateur* (UID).
*Parfois,* pour des raisons actuellement inconnues, lors de l’installation sur une application déjà installée, l’installation échouera avec `INSTALL_FAILED_UID_CHANGED`:

```shell
ERROR [2015-03-23 11:19:01Z]: ANDROID: Deployment failed
Mono.AndroidTools.InstallFailedException: Failure [INSTALL_FAILED_UID_CHANGED]
   at Mono.AndroidTools.Internal.AdbOutputParsing.CheckInstallSuccess(String output, String packageName)
   at Mono.AndroidTools.AndroidDevice.<>c__DisplayClass2c.<InstallPackage>b__2b(Task`1 t)
   at System.Threading.Tasks.ContinuationTaskFromResultTask`1.InnerInvoke()
   at System.Threading.Tasks.Task.Execute()
```

Pour contourner ce problème, *désinstaller complètement* le paquet Android, soit en installant l’application à partir de l’interface graphique de la cible Android, soit en utilisant `adb`:

```shell
$ adb uninstall @PACKAGE_NAME@
```

**NE PAS USE** `adb uninstall -k`, car cela *préservera les* données d’application, et ainsi préserver l’UID contradictoire sur le dispositif cible.

## <a name="release-apps-fail-to-launch-on-device"></a>Les applications de sortie ne parviennent pas à lancer sur l’appareil

Est-ce que la sortie Android Debug Log contiendra un message similaire à:

```shell
D/AndroidRuntime( 1710): Shutting down VM
W/dalvikvm( 1710): threadid=1: thread exiting with uncaught exception (group=0xb412f180)
E/AndroidRuntime( 1710): FATAL EXCEPTION: main
E/AndroidRuntime( 1710): java.lang.UnsatisfiedLinkError: Couldn't load monodroid: findLibrary returned null
E/AndroidRuntime( 1710):        at java.lang.Runtime.loadLibrary(Runtime.java:365)
```

Si c’est le cas, il y a deux causes possibles à cela :

1. Le .apk ne fournit pas un ABI que l’appareil cible prend en charge.
    Par exemple, le .apk ne contient que des binaires armeabi-v7a, et le dispositif cible ne prend en charge armeabi.

2. Un [bug Android](https://code.google.com/p/android/issues/detail?id=21670). Si c’est le cas, désinstaller l’application, croisez les doigts et réinstallez l’application.

Pour corriger (1), modifier les options/propriétés du projet et [ajouter la prise en charge de l’ABI requise à la liste des ATI pris en charge](~/android/app-fundamentals/cpu-architectures.md).Pour déterminer l’ABI que vous devez ajouter, exécutez la commande de bad suivante contre votre appareil cible :

```shell
adb shell getprop ro.product.cpu.abi
adb shell getprop ro.product.cpu.abi2
```

La sortie contiendra les ISC primaires (et facultatifs secondaires).

```shell
$ adb shell getprop | grep ro.product.cpu
[ro.product.cpu.abi2]: [armeabi]
[ro.product.cpu.abi]: [armeabi-v7a]
```

## <a name="the-outpath-property-is-not-set-for-project-ldquomyappcsprojrdquo"></a>La propriété OutPath n’est pas définie pour le projet &ldquo;MyApp.csproj&rdquo;

Cela signifie généralement que vous avez &ldquo;un&rdquo; ordinateur HP et la plate-forme variable de l’environnement a été réglé à quelque chose comme MCD ou HPD. Cela entre en conflit avec la propriété MSBuild&rdquo; &ldquo;Platform qui&rdquo;est généralement réglé sur &ldquo;tout processeur ou x86 . Vous devrez supprimer cette variable d’environnement de votre machine avant que MSBuild puisse fonctionner :

- Control Panel > System > Advanced > Environment Variables

Redémarrez Visual Studio ou Visual Studio pour Mac et essayez de reconstruire. Les choses devraient maintenant fonctionner comme prévu.

## <a name="javalangclasscastexception-monoandroidruntimejavaobject-cannot-be-cast-to"></a>java.lang.ClassCastException: mono.android.runtime.JavaObject ne peut pas être jeté à ...

Xamarin.Android 4.x ne marshal correctement les types génériques imbriqués correctement. Par exemple, considérez\# le code C suivant à l’aide [de SimpleExpandableListAdapter](xref:Android.Widget.SimpleExpandableListAdapter):

```csharp
// BAD CODE; DO NOT USE
var groupData = new List<IDictionary<string, object>> () {
        new Dictionary<string, object> {
                { "NAME", "Group 1" },
                { "IS_EVEN", "This group is odd" },
        },
};
var childData = new List<IList<IDictionary<string, object>>> () {
        new List<IDictionary<string, object>> {
                new Dictionary<string, object> {
                        { "NAME", "Child 1" },
                        { "IS_EVEN", "This group is odd" },
                },
        },
};
mAdapter = new SimpleExpandableListAdapter (
        this,
        groupData,
        Android.Resource.Layout.SimpleExpandableListItem1,
        new string[] { "NAME", "IS_EVEN" },
        new int[] { Android.Resource.Id.Text1, Android.Resource.Id.Text2 },
        childData,
        Android.Resource.Layout.SimpleExpandableListItem2,
        new string[] { "NAME", "IS_EVEN" },
        new int[] { Android.Resource.Id.Text1, Android.Resource.Id.Text2 }
);
```

Le problème est que Xamarin.Android mal marshals niché types génériques. Le `List<IDictionary<string, object>>` est en cours de maréchal à un [java.lang.ArrrayList](xref:Java.Util.ArrayList), mais `ArrayList` l’est contenant des `mono.android.runtime.JavaObject` instances (qui font référence aux `Dictionary<string, object>` instances) au lieu de quelque chose qui implémente [java.util.Map](xref:Java.Util.IMap), résultant en l’exception suivante:

```shell
E/AndroidRuntime( 2991): FATAL EXCEPTION: main
E/AndroidRuntime( 2991): java.lang.ClassCastException: mono.android.runtime.JavaObject cannot be cast to java.util.Map
E/AndroidRuntime( 2991):        at android.widget.SimpleExpandableListAdapter.getGroupView(SimpleExpandableListAdapter.java:278)
E/AndroidRuntime( 2991):        at android.widget.ExpandableListConnector.getView(ExpandableListConnector.java:446)
E/AndroidRuntime( 2991):        at android.widget.AbsListView.obtainView(AbsListView.java:2271)
E/AndroidRuntime( 2991):        at android.widget.ListView.makeAndAddView(ListView.java:1769)
E/AndroidRuntime( 2991):        at android.widget.ListView.fillDown(ListView.java:672)
E/AndroidRuntime( 2991):        at android.widget.ListView.fillFromTop(ListView.java:733)
E/AndroidRuntime( 2991):        at android.widget.ListView.layoutChildren(ListView.java:1622)
```

La solution de contournement est d’utiliser les &ldquo;types&rdquo; de [collection Java](~/android/internals/api-design.md) fournis au lieu des `System.Collections.Generic` types pour les types intérieurs. Cela se traduira par des types Java appropriés lors de la mise en œuvre des instances. (Le code suivant est plus compliqué que nécessaire afin de réduire les durées de vie des grefs. Il peut être simplifié pour modifier `s/List/JavaList/g` `s/Dictionary/JavaDictionary/g` le code d’origine via et si les durées de vie gref ne sont pas un souci.)

```csharp
// insert good code here
using (var groupData = new JavaList<IDictionary<string, object>> ()) {
    using (var groupEntry = new JavaDictionary<string, object> ()) {
        groupEntry.Add ("NAME", "Group 1");
        groupEntry.Add ("IS_EVEN", "This group is odd");
        groupData.Add (groupEntry);
    }
    using (var childData = new JavaList<IList<IDictionary<string, object>>> ()) {
        using (var childEntry = new JavaList<IDictionary<string, object>> ())
        using (var childEntryDict = new JavaDictionary<string, object> ()) {
            childEntryDict.Add ("NAME", "Child 1");
            childEntryDict.Add ("IS_EVEN", "This child is odd.");
            childEntry.Add (childEntryDict);
            childData.Add (childEntry);
        }
        mAdapter = new SimpleExpandableListAdapter (
            this,
            groupData,
            Android.Resource.Layout.SimpleExpandableListItem1,
            new string[] { "NAME", "IS_EVEN" },
            new int[] { Android.Resource.Id.Text1, Android.Resource.Id.Text2 },
            childData,
            Android.Resource.Layout.SimpleExpandableListItem2,
            new string[] { "NAME", "IS_EVEN" },
            new int[] { Android.Resource.Id.Text1, Android.Resource.Id.Text2 }
        );
    }
}
```

[Ceci sera fixé dans une prochaine version](https://bugzilla.xamarin.com/show_bug.cgi?id=5401).

## <a name="unexpected-nullreferenceexceptions"></a>NullReferenceExceptions inattendus

Occasionnellement, le [journal Android Debug](~/android/deploy-test/debugging/android-debug-log.md) mentionnera &ldquo;NullReferenceExceptions qui ne peuvent pas se produire,&rdquo; ou proviennent de Mono pour le code Android runtime peu de temps avant que l’application meurt:

```shell
E/mono(15202): Unhandled Exception: System.NullReferenceException: Object reference not set to an instance of an object
E/mono(15202):   at Java.Lang.Object.GetObject (IntPtr handle, System.Type type, Boolean owned)
E/mono(15202):   at Java.Lang.Object._GetObject[IOnTouchListener] (IntPtr handle, Boolean owned)
E/mono(15202):   at Java.Lang.Object.GetObject[IOnTouchListener] (IntPtr handle, Boolean owned)
E/mono(15202):   at Android.Views.View+IOnTouchListenerAdapter.n_OnTouch_Landroid_view_View_Landroid_view_MotionEvent_(IntPtr jnienv, IntPtr native__this, IntPtr native_v, IntPtr native_e)
E/mono(15202):   at (wrapper dynamic-method) object:b039cbb0-15e9-4f47-87ce-442060701362 (intptr,intptr,intptr,intptr)
```

or

```shell
E/mono    ( 4176): Unhandled Exception:
E/mono    ( 4176): System.NullReferenceException: Object reference not set to an instance of an object
E/mono    ( 4176): at Android.Runtime.JNIEnv.NewString (string)
E/mono    ( 4176): at Android.Util.Log.Info (string,string)
```

Cela peut se produire lorsque l’exécution Android décide d’interrompre le processus, ce qui peut arriver &ldquo;pour&rdquo; un certain nombre de raisons, y compris frapper la limite GREF de la cible ou faire quelque chose de mal avec JNI.

Pour voir si c’est le cas, consultez le Journal Android Debug pour un message de votre processus similaire à:

```shell
E/dalvikvm(  123): VM aborting
```

## <a name="abort-due-to-global-reference-exhaustion"></a>Avorter en raison de l’épuisement de référence mondiale

La couche JNI de l’Android ne prend en charge qu’un nombre limité de références d’objets JNI pour être valides à un moment donné. Lorsque cette limite est dépassée, les choses se brisent.

La limite GREF *(référence globale)* est de 2000 références dans l’émulateur, et de 52000 références sur le matériel.

Vous savez que vous commencez à créer trop de GREFs lorsque vous voyez des messages comme celui-ci dans le journal Android Debug:

```shell
D/dalvikvm(  602): GREF has increased to 1801
```

Lorsque vous atteignez la limite GREF, un message tel que ce qui suit est imprimé :

```shell
D/dalvikvm(  602): GREF has increased to 2001
W/dalvikvm(  602): Last 10 entries in JNI global reference table:
W/dalvikvm(  602):  1991: 0x4057eff8 cls=Landroid/graphics/Point; (20 bytes)
W/dalvikvm(  602):  1992: 0x4057f010 cls=Landroid/graphics/Point; (28 bytes)
W/dalvikvm(  602):  1993: 0x40698e70 cls=Landroid/graphics/Point; (20 bytes)
W/dalvikvm(  602):  1994: 0x40698e88 cls=Landroid/graphics/Point; (20 bytes)
W/dalvikvm(  602):  1995: 0x40698ea0 cls=Landroid/graphics/Point; (28 bytes)
W/dalvikvm(  602):  1996: 0x406981f0 cls=Landroid/graphics/Point; (20 bytes)
W/dalvikvm(  602):  1997: 0x40698208 cls=Landroid/graphics/Point; (20 bytes)
W/dalvikvm(  602):  1998: 0x40698220 cls=Landroid/graphics/Point; (28 bytes)
W/dalvikvm(  602):  1999: 0x406956a8 cls=Landroid/graphics/Point; (20 bytes)
W/dalvikvm(  602):  2000: 0x406956c0 cls=Landroid/graphics/Point; (20 bytes)
W/dalvikvm(  602): JNI global reference table summary (2001 entries):
W/dalvikvm(  602):    51 of Ljava/lang/Class; 164B (41 unique)
W/dalvikvm(  602):    46 of Ljava/lang/Class; 188B (17 unique)
W/dalvikvm(  602):     6 of Ljava/lang/Class; 212B (6 unique)
W/dalvikvm(  602):    11 of Ljava/lang/Class; 236B (7 unique)
W/dalvikvm(  602):     3 of Ljava/lang/Class; 260B (3 unique)
W/dalvikvm(  602):     4 of Ljava/lang/Class; 284B (2 unique)
W/dalvikvm(  602):     8 of Ljava/lang/Class; 308B (6 unique)
W/dalvikvm(  602):     1 of Ljava/lang/Class; 316B
W/dalvikvm(  602):     4 of Ljava/lang/Class; 332B (3 unique)
W/dalvikvm(  602):     1 of Ljava/lang/Class; 356B
W/dalvikvm(  602):     2 of Ljava/lang/Class; 380B (1 unique)
W/dalvikvm(  602):     1 of Ljava/lang/Class; 428B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 452B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 476B
W/dalvikvm(  602):     2 of Ljava/lang/Class; 500B (1 unique)
W/dalvikvm(  602):     1 of Ljava/lang/Class; 548B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 572B
W/dalvikvm(  602):     2 of Ljava/lang/Class; 596B (2 unique)
W/dalvikvm(  602):     1 of Ljava/lang/Class; 692B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 956B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 1004B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 1148B
W/dalvikvm(  602):     2 of Ljava/lang/Class; 1172B (1 unique)
W/dalvikvm(  602):     1 of Ljava/lang/Class; 1316B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 3428B
W/dalvikvm(  602):     1 of Ljava/lang/Class; 3452B
W/dalvikvm(  602):     1 of Ljava/lang/String; 28B
W/dalvikvm(  602):     2 of Ldalvik/system/VMRuntime; 12B (1 unique)
W/dalvikvm(  602):    10 of Ljava/lang/ref/WeakReference; 28B (10 unique)
W/dalvikvm(  602):     1 of Ldalvik/system/PathClassLoader; 44B
W/dalvikvm(  602):  1553 of Landroid/graphics/Point; 20B (1553 unique)
W/dalvikvm(  602):   261 of Landroid/graphics/Point; 28B (261 unique)
W/dalvikvm(  602):     1 of Landroid/view/MotionEvent; 100B
W/dalvikvm(  602):     1 of Landroid/app/ActivityThread$ApplicationThread; 28B
W/dalvikvm(  602):     1 of Landroid/content/ContentProvider$Transport; 28B
W/dalvikvm(  602):     1 of Landroid/view/Surface$CompatibleCanvas; 44B
W/dalvikvm(  602):     1 of Landroid/view/inputmethod/InputMethodManager$ControlledInputConnectionWrapper; 36B
W/dalvikvm(  602):     1 of Landroid/view/ViewRoot$1; 12B
W/dalvikvm(  602):     1 of Landroid/view/ViewRoot$W; 28B
W/dalvikvm(  602):     1 of Landroid/view/inputmethod/InputMethodManager$1; 28B
W/dalvikvm(  602):     1 of Landroid/view/accessibility/AccessibilityManager$1; 28B
W/dalvikvm(  602):     1 of Landroid/widget/LinearLayout$LayoutParams; 44B
W/dalvikvm(  602):     1 of Landroid/widget/LinearLayout; 332B
W/dalvikvm(  602):     2 of Lorg/apache/harmony/xnet/provider/jsse/TrustManagerImpl; 28B (1 unique)
W/dalvikvm(  602):     1 of Landroid/view/SurfaceView$MyWindow; 36B
W/dalvikvm(  602):     1 of Ltouchtest/RenderThread; 92B
W/dalvikvm(  602):     1 of Landroid/view/SurfaceView$3; 12B
W/dalvikvm(  602):     1 of Ltouchtest/DrawingView; 412B
W/dalvikvm(  602):     1 of Ltouchtest/Activity1; 180B
W/dalvikvm(  602): Memory held directly by tracked refs is 75624 bytes
E/dalvikvm(  602): Excessive JNI global references (2001)
E/dalvikvm(  602): VM aborting
```

Dans l’exemple ci-dessus (qui, soit dit en passant, vient de [bug 685215](https://bugzilla.novell.com/show_bug.cgi?id=685215)) le problème est que trop d’instances Android.Graphics.Point sont en cours de création; voir [ \#le commentaire 2](https://bugzilla.novell.com/show_bug.cgi?id=685215#c2) pour une liste de correctifs pour ce bogue particulier.

Typiquement, une solution utile est de trouver quel &ndash; type a trop d’instances &ndash; allouées Android.Graphics.Point dans le dépotoir ci-dessus, puis trouver où ils sont créés dans votre code source et les disposer de manière appropriée (de sorte que leur durée de vie Java-objet est raccourci). Ce n’est\#pas toujours approprié ( 685215 est relu plusieurs fois, de sorte que la solution triviale évite l’appel Dispose), mais c’est la première chose à considérer.

Vous pouvez activer [GREF Logging](~/android/troubleshooting/index.md) pour voir quand les GREF sont créés et combien existent.

## <a name="abort-due-to-jni-type-mismatch"></a>Avorter en raison de l’inadéquation de type JNI

Si vous roulez à la main le code JNI, il est possible que les `java.lang.Runnable.run` types ne correspondent pas `java.lang.Runnable`correctement, par exemple si vous essayez d’invoquer sur un type qui ne met pas en œuvre . Lorsque cela se produit, il y aura un message similaire à celui-ci dans le journal Android Debug:

```shell
W/dalvikvm( 123): JNI WARNING: can't call Ljava/Type;;.method on instance of Lanother/java/Type;
W/dalvikvm( 123):              in Lmono/java/lang/RunnableImplementor;.n_run:()V (CallVoidMethodA)
...
E/dalvikvm( 123): VM aborting
```

## <a name="dynamic-code-support"></a>Soutien dynamique au code

### <a name="dynamic-code-does-not-compile"></a>Le code dynamique ne compile pas

Pour utiliser\# la dynamique C dans votre application ou bibliothèque, vous devez ajouter System.Core.dll, Microsoft.CSharp.dll et Mono.CSharp.dll à votre projet.

### <a name="in-release-build-missingmethodexception-occurs-for-dynamic-code-at-run-time"></a>Dans la version de release, MissingMethodException se produit pour le code dynamique au moment de l’exécution.

- Il est probable que votre projet d’application n’a pas de références à System.Core.dll, Microsoft.CSharp.dll ou Mono.CSharp.dll. Assurez-vous que ces assemblages sont référencés.

  - Gardez à l’esprit que le code dynamique coûte toujours. Si vous avez besoin d’un code efficace, envisagez de ne pas utiliser de code dynamique.

- Dans le premier aperçu, ces assemblages ont été exclus à moins que les types de chaque assemblage ne soient explicitement utilisés par le code d’application. Voir ce qui suit pour une solution de contournement:[http://lists.ximian.com/pipermail/mo...il/009798.html](http://lists.ximian.com/pipermail/monodroid/2012-April/009798.html)

## <a name="projects-built-with-aotllvm-crash-on-x86-devices"></a>Projets construits avec crash AOT-LLVM sur les appareils x86

Lors du déploiement d’une application conçue avec [AOT-LLVM](~/android/deploy-test/release-prep/index.md) sur les appareils x86, vous pouvez voir un message d’erreur d’exception similaire à ce qui suit :

```shell
Assertion: should not be reached at /Users/.../external/mono/mono/mini/tramp-x86.c:124
Fatal signal 6 (SIGABRT), code -6 in tid 4051 (Xamarin.bug56111)
```

Il s’agit d’un problème connu - la solution de contournement est de désactiver LLVM.
