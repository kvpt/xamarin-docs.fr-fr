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
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2020
ms.locfileid: "78912324"
---
# <a name="troubleshooting-tips"></a>Conseils de dépannage

## <a name="getting-diagnostic-information"></a>Obtention d’informations de diagnostic

Xamarin. Android présente quelques emplacements à examiner pour le suivi de divers bogues.
Elles incluent notamment les suivantes :

1. Sortie MSBuild de diagnostic.
2. Journaux de déploiement de l’appareil.
3. Sortie du journal de débogage Android.

<a name="Diagnostic_MSBuild_Output" />

## <a name="diagnostic-msbuild-output"></a>Diagnostic de la sortie MSBuild

Diagnostic MSBuild peut contenir des informations supplémentaires relatives à la génération de packages et peut contenir des informations de déploiement de package.

Pour activer la sortie MSBuild de diagnostic dans Visual Studio :

1. Cliquez sur **outils > options...**
2. Dans l’arborescence de gauche, sélectionnez **projets et Solutions > générer et exécuter**
3. Dans le volet de droite, définissez la liste déroulante détail de la sortie de génération MSBuild sur diagnostic
4. Cliquez sur **OK**.
5. Nettoyez et regénérez votre package.
6. La sortie de diagnostic est visible dans le panneau sortie.

Pour activer la sortie MSBuild de diagnostic dans Visual Studio pour Mac/OS X :

1. Cliquez sur **Visual Studio pour Mac > préférences...**
2. Dans l’arborescence de gauche, sélectionnez **projets > générer**
3. Dans le volet de droite, définissez la liste déroulante commentaires du journal sur diagnostic
4. Cliquez sur **OK**.
5. Redémarrer Visual Studio pour Mac
6. Nettoyez et regénérez votre package.
7. La sortie de diagnostic est visible dans le bloc d’erreurs (**affichez > > Erreurs** ) en cliquant sur le bouton sortie de la génération.

## <a name="device-deployment-logs"></a>Journaux de déploiement de l’appareil

Pour activer la journalisation du déploiement des appareils dans Visual Studio :

1. **Outils > options...** >
2. Dans l’arborescence de gauche, sélectionnez **Xamarin > paramètres Android**
3. Dans le volet de droite, activez la case à cocher **journalisation du débogage de l’extension [X] (écrit monodroid. log sur votre bureau)** .
4. Les messages de journal sont écrits dans le fichier monodroid. log sur votre bureau.

Visual Studio pour Mac écrit toujours les journaux de déploiement d’appareil. Leur recherche est un peu plus difficile. un fichier journal *AndroidUtils* est créé pour chaque jour + heure à laquelle un déploiement a lieu, par exemple : **AndroidTools-2012-10 -24 _12-35 -45. log**.

- Sur Windows, les fichiers journaux sont écrits dans `%LOCALAPPDATA%\XamarinStudio-{VERSION}\Logs`.
- Sur OS X, les fichiers journaux sont écrits dans `$HOME/Library/Logs/XamarinStudio-{VERSION}`.

## <a name="android-debug-log-output"></a>Sortie du journal de débogage Android

Android écrira de nombreux messages dans le [Journal de débogage Android](~/android/deploy-test/debugging/android-debug-log.md).
Xamarin. Android utilise les propriétés système Android pour contrôler la génération de messages supplémentaires dans le journal de débogage Android. Les propriétés du système Android peuvent être définies à l’aide de la commande *échec SetProp* dans le [Android Debug Bridge (ADB)](https://developer.android.com/guide/developing/tools/adb.html):

```shell
adb shell setprop PROPERTY_NAME PROPERTY_VALUE
```

Les propriétés système sont lues pendant le démarrage du processus et doivent donc être définies avant que l’application soit lancée ou l’application doit être redémarrée après la modification des propriétés système.

### <a name="xamarinandroid-system-properties"></a>Propriétés système de Xamarin.Android

Xamarin. Android prend en charge les propriétés système suivantes :

- *Debug. mono. Debug*: si une chaîne non vide est, cela équivaut à `*mono-debug*`.

- *Debug. mono. env*: une liste de variables d’environnement séparées par des barres verticales (« *|* ») à exporter au cours du démarrage de l’application, *avant* l’initialisation de mono. Cela permet de définir des variables d’environnement qui contrôlent la journalisation mono.

  > [!NOTE]
  > Étant donné que la valeur est « *|* »-séparé, la valeur doit avoir un niveau supplémentaire de guillemets, car la commande \`*shell adb*\` supprimera un ensemble de guillemets.

  > [!NOTE]
  > Les valeurs de propriété du système Android ne peuvent pas dépasser 92 caractères.

  Exemple :

  ```
  adb shell setprop debug.mono.env "'MONO_LOG_LEVEL=info|MONO_LOG_MASK=asm'"
  ```

- *Debug. mono. log*: liste séparée par des virgules *(«»* ) des composants qui doivent imprimer des messages supplémentaires dans le journal de débogage Android. Par défaut, rien n’est défini. Les composants sont les suivants :

  - *tout*: imprimer tous les messages
  - *GC*: imprimer les messages relatifs au gc.
  - *Gref*: imprime les messages d’allocation et de désallocation de référence (faibles, globaux).
  - *lref*: imprimer les messages d’allocation et de désallocation des références locales.

  > [!NOTE]
  > Ils sont *extrêmement* détaillés. N’activez pas, sauf si vous en avez vraiment besoin.

- *Debug. mono. trace*: permet de définir le paramètre [mono--trace](http://docs.go-mono.com/?link=man%3amono(1))`=PROPERTY_VALUE`.

## <a name="deleting-bin-and-obj"></a>Suppression des `bin` et des `obj`

Xamarin. Android a subi par le passé une situation telle que :

- Vous rencontrez une erreur de build ou d’exécution étrange.
- Vous `Clean`, `Rebuild`ou supprimer manuellement vos répertoires `bin` et `obj`.
- Le problème disparaît.

Nous avons beaucoup investi dans la résolution des problèmes tels que ceux liés à leur impact sur la productivité des développeurs.

Si cela vous pose un problème :

1. Prenez une note mentale. Quelle était la dernière action qui a obtenu votre projet dans cet État ?
1. Enregistrez votre journal de génération actuel. Réessayez de générer et enregistrez un [Journal de génération de diagnostic](#diagnostic-msbuild-output).
1. Soumettre un [rapport de bogue][bug].

Avant de supprimer vos répertoires `bin` et `obj`, compressez-les et enregistrez-les pour les diagnostics ultérieurs, si nécessaire. Vous pouvez probablement simplement `Clean` votre projet d’application Xamarin. Android pour faire fonctionner à nouveau les choses.

[bug]: https://github.com/xamarin/xamarin-android/wiki/Submitting-Bugs,-Feature-Requests,-and-Pull-Requests

## <a name="xamarinandroid-cannot-resolve-systemvaluetuple"></a>Xamarin. Android ne peut pas résoudre System. ValueTuple

Cette erreur se produit en raison d’une incompatibilité avec Visual Studio.

- **Visual Studio 2017 Update 1** (version 15,1 ou antérieure) est compatible uniquement avec le **système. ValueTuple NuGet 4.3.0** (ou antérieur).

- **Visual Studio 2017 Update 2** (version 15,2 ou ultérieure) est compatible uniquement avec le **System. ValueTuple NuGet 4.3.1** (ou une version ultérieure).

Veuillez choisir le système. ValueTuple NuGet approprié qui correspond à votre installation de Visual Studio 2017.

## <a name="gc-messages"></a>Messages GC

Les messages du composant GC peuvent être affichés en affectant à la propriété système Debug. mono. log une valeur qui contient gc.

Les messages de GC sont générés à chaque exécution du GC et fournissent des informations sur la quantité de travail du GC :

```shell
I/monodroid-gc(12331): GC cleanup summary: 81 objects tested - resurrecting 21.
```

Des informations supplémentaires sur le GC, telles que les informations de minutage, peuvent être générées en définissant la variable d’environnement `MONO_LOG_LEVEL` sur `debug`:

```shell
adb shell setprop debug.mono.env MONO_LOG_LEVEL=debug
```

Cela entraînera (un grand nombre de) messages mono supplémentaires, notamment les trois conséquences suivantes :

```shell
D/Mono (15723): GC_BRIDGE num-objects 1 num_hash_entries 81226 sccs size 81223 init 0.00ms df1 285.36ms sort 38.56ms dfs2 50.04ms setup-cb 9.95ms free-data 106.54ms user-cb 20.12ms clenanup 0.05ms links 5523436/5523436/5523096/1 dfs passes 1104 6883/11046605
D/Mono (15723): GC_MINOR: (Nursery full) pause 2.01ms, total 287.45ms, bridge 225.60 promoted 0K major 325184K los 1816K
D/Mono ( 2073): GC_MAJOR: (user request) pause 2.17ms, total 2.47ms, bridge 28.77 major 576K/576K los 0K/16K
```

Dans le message `GC_BRIDGE`, `num-objects` est le nombre d’objets de pont envisagés par cette passe, et `num_hash_entries` le nombre d’objets traités au cours de cet appel du code de pont.

Dans les messages `GC_MINOR` et `GC_MAJOR`, `total` est le laps de temps pendant lequel le monde est suspendu (aucun thread n’est en cours d’exécution), tandis que `bridge` est le temps nécessaire dans le code de traitement de pont (qui traite la machine virtuelle Java). Le monde n’est *pas* mis en suspens pendant le traitement du pont.

 *En règle générale*, plus la valeur de `num_hash_entries`est grande, plus le nombre de regroupements `bridge` est élevé, et plus le temps `total` de collecte sera important.

## <a name="global-reference-messages"></a>Messages de référence globaux

Pour activer la journalisation globale des loggig (GREF), la propriété système *Debug. mono. log* doit contenir *Gref*, par exemple :

```shell
adb shell setprop debug.mono.log gref
```

Xamarin. Android utilise des références internationales Android pour fournir des mappages entre les instances Java et les instances gérées associées, comme lors de l’appel d’une méthode Java, une instance Java doit être fournie à Java.

Malheureusement, les émulateurs Android n’autorisent l’existence que de 2000 références globales à la fois. Le matériel a une limite plus élevée de 52000 références globales. La limite inférieure peut être problématique lors de l’exécution d’applications sur l’émulateur. par conséquent, il peut être très utile *de connaître l’origine de* l’instance.

> [!NOTE]
> Le décompte de références global est interne à Xamarin. Android et n’inclut pas (et ne peut pas) les références globales extraites par d’autres bibliothèques natives chargées dans le processus. Utilisez le nombre de références global comme estimation.

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

Il y a quatre messages de conséquence :

- Création de références globales : il s’agit des lignes qui commencent par *+ g +* et qui fourniront une trace de la pile pour le chemin de code de création.
- Destruction de référence globale : il s’agit des lignes qui commencent par *-g-* et peuvent fournir une trace de la pile pour le chemin de code en supprimant la référence globale. Si le GC supprime le Gref, aucune trace de la pile ne sera fournie.
- Création de références globales faible : il s’agit des lignes qui commencent par *+ w +* .
- Destruction de référence globale faible : il s’agit de lignes qui commencent par *-w-* .

Dans tous les messages, la valeur *grefc* est le nombre de références globales créées par Xamarin. Android, tandis que la valeur *grefwc* est le nombre de références globales faibles que Xamarin. Android a créées. La valeur *handle* ou *obj-handle* est la valeur de handle JNI, et le caractère après' */* 'est le type de valeur de handle : */l* pour référence locale, */g* pour les références globales et */w* pour les références globales faibles.

Dans le cadre du processus GC, les références globales (+ g +) sont converties en références globales faibles (provoquant + w + et-g-), un GC côté Java est lancé, puis la référence mondiale faible est vérifiée pour voir s’il a été collecté. S’il est toujours actif, une nouvelle Gref est créée autour de la référence faible (+ g +,-w-), sinon la référence faible est détruite (-w).

## <a name="java-instance-is-created-and-wrapped-by-a-mcw"></a>L’instance Java est créée et incluse dans un wrapper par un MCW

```shell
I/monodroid-gref(27679): +g+ grefc 2211 gwrefc 0 obj-handle 0x4066df10/L -> new-handle 0x4066df10/L from ...
I/monodroid-gref(27679): handle 0x4066df10; key_handle 0x4066df10: Java Type: `android/graphics/drawable/TransitionDrawable`; MCW type: `Android.Graphics.Drawables.TransitionDrawable`
```

## <a name="a-gc-is-being-performed"></a>Un GC est en cours d’exécution...

```shell
I/monodroid-gref(27679): +w+ grefc 1953 gwrefc 259 obj-handle 0x4066df10/G -> new-handle 0xde68f95f/W from take_weak_global_ref_jni
I/monodroid-gref(27679): -g- grefc 1952 gwrefc 259 handle 0x4066df10/G from take_weak_global_ref_jni
```

## <a name="object-is-still-alive-as-handle--null"></a>L’objet est toujours actif, comme handle ! = null
## <a name="wref-turned-back-into-a-gref"></a>Wref reconverti en Gref

```shell
I/monodroid-gref(27679): *try_take_global obj=0x4976f080 -> wref=0xde68f95f handle=0x4066df10
I/monodroid-gref(27679): +g+ grefc 1930 gwrefc 39 obj-handle 0xde68f95f/W -> new-handle 0x4066df10/G from take_global_ref_jni
I/monodroid-gref(27679): -w- grefc 1930 gwrefc 38 handle 0xde68f95f/W from take_global_ref_jni
```

## <a name="object-is-dead-as-handle--null"></a>L’objet est inactif, car handle = = null
## <a name="wref-is-freed-no-new-gref-created"></a>Wref est libéré, aucun nouveau Gref n’a été créé

```shell
I/monodroid-gref(27679): *try_take_global obj=0x4976f080 -> wref=0xde68f95f handle=0x0
I/monodroid-gref(27679): -w- grefc 1914 gwrefc 296 handle 0xde68f95f/W from take_global_ref_jni
```

Il y a une ride « intéressante » ici : sur les cibles exécutant Android avant 4,0, la valeur Gref est égale à l’adresse de l’objet Java dans la mémoire du runtime Android. (Autrement dit, le GC est un collecteur qui n’est pas en déplacement, conservateur et qui consiste à transmettre des références directes à ces objets.) Ainsi, après a + g +, + w +,-g-, + g +,-w-Sequence, le Gref résultant aura la même valeur que la valeur Gref d’origine. Cela rend Grepping relativement simple dans les journaux.

Android 4,0, cependant, dispose d’un collecteur mobile qui ne transmet plus de références directes aux objets de machine virtuelle du runtime Android. Par conséquent, après a + g +, + w +,-g-, + g +,-w-Sequence, la valeur Gref *sera différente*. Si l’objet survit à plusieurs catalogues globaux, il passe par plusieurs valeurs Gref, ce qui rend plus difficile la détermination de l’emplacement où une instance a été réellement allouée.

### <a name="querying-programmatically"></a>Interrogation par programmation

Vous pouvez interroger les nombres GREF et WREF en interrogeant l’objet `JniRuntime`.

`Java.Interop.JniRuntime.CurrentRuntime.GlobalReferenceCount`-nombre de références globales

`Java.Interop.JniRuntime.CurrentRuntime.WeakGlobalReferenceCount`-nombre de références faible

## <a name="android-debug-logs"></a>Journaux de débogage Android

Les [journaux de débogage Android](~/android/deploy-test/debugging/android-debug-log.md) peuvent fournir un contexte supplémentaire concernant les erreurs d’exécution que vous rencontrez.

## <a name="floating-point-performance-is-terrible"></a>Les performances à virgule flottante sont terribles !

Sinon, « mon application s’exécute 10 fois plus rapidement avec la version Debug qu’avec la version Release ! »

Xamarin. Android prend en charge plusieurs Abi d’appareils : *ARMEABI*, *ARMEABI-V7A*et *x86*. Les Abi d’appareil peuvent être spécifiés dans les **Propriétés du projet > onglet Application > architectures prises en charge**.

Les versions Debug utilisent un package Android qui fournit toutes les Abi, et utilise donc le ABI le plus rapide pour l’appareil cible.

Les versions release incluent uniquement les Abi sélectionnés dans l’onglet Propriétés du projet. Plusieurs peuvent être sélectionnés.

*ARMEABI* est l’Abi par défaut et possède la prise en charge d’appareils la plus large. *Toutefois*, ARMEABI ne prend pas en charge les appareils à plusieurs UC et le matériel à virgule flottante, amont autres choses. Par conséquent, les applications utilisant le runtime de la version ARMEABI sont liées à un seul cœur et utilisent une implémentation floue. Ces deux options peuvent contribuer à réduire considérablement les performances de votre application.

Si votre application requiert des performances à virgule flottante correctes (par exemple, des jeux), vous devez activer l’Abi *ARMEABI-V7A* . Vous pouvez uniquement prendre en charge le runtime *ARMEABI-V7A* , bien que cela signifie que les appareils plus anciens qui prennent uniquement en charge *ARMEABI* ne seront pas en mesure d’exécuter votre application.

## <a name="could-not-locate-android-sdk"></a>Android SDK introuvable

Il existe 2 téléchargements disponibles à partir de Google pour le Android SDK pour Windows.
Si vous choisissez le programme d’installation. exe, il écrira des clés de Registre qui indiquent à Xamarin. Android où il a été installé. Si vous choisissez le fichier. zip et que vous le Décompressez vous-même, Xamarin. Android ne sait pas où rechercher le kit de développement logiciel (SDK). Vous pouvez indiquer à Xamarin. Android l’emplacement du kit de développement logiciel (SDK) dans Visual Studio en accédant à **outils > Options > Xamarin > paramètres Android**:

[![Android SDK emplacement dans les paramètres Xamarin Android](troubleshooting-images/01.png)](troubleshooting-images/01.png#lightbox)

## <a name="ide-does-not-display-target-device"></a>L’IDE n’affiche pas l’appareil cible

Vous allez parfois essayer de déployer votre application sur un appareil, mais l’appareil que vous souhaitez déployer n’apparaît pas dans la boîte de dialogue Sélectionner un périphérique. Cela peut se produire lorsque le Android Debug Bridge décide de passer en vacances.

Pour diagnostiquer ce problème, recherchez le [programme ADB](~/android/deploy-test/debugging/android-debug-log.md), puis exécutez :

```shell
adb devices
```

Si votre appareil n’est pas présent, vous devez redémarrer le serveur Android Debug Bridge pour que votre appareil puisse être trouvé :

```shell
adb kill-server
adb start-server
```

Le logiciel de synchronisation HTC peut empêcher le fonctionnement correct du **serveur ADB Start-Server** . Si la commande **ADB Start-Server** n’affiche pas le port sur lequel elle commence, quittez le logiciel de synchronisation HTC et essayez de redémarrer le serveur ADB.

## <a name="the-specified-task-executable-keytool-could-not-be-run"></a>Impossible d’exécuter l’exécutable de tâche spécifié « keytool »

Cela signifie que votre chemin d’accès ne contient pas le répertoire dans lequel se trouve le répertoire bin du kit de développement logiciel (SDK) Java. Vérifiez que vous avez suivi les étapes décrites dans le Guide d' [installation](~/android/get-started/installation/index.md) .

## <a name="monodroidexe-or-aresgenexe-exited-with-code-1"></a>monodroid. exe ou aresgen. exe s’est arrêté avec le code 1

Pour vous aider à déboguer ce problème, accédez à Visual Studio et modifiez le niveau de détail MSBuild. pour ce faire, sélectionnez **outils > Options > projet** et **solutions > générer** et **Exécuter > Détails de la sortie de génération du projet MSBuild** et définir cette valeur sur **normal**.

Régénérez et vérifiez le volet de sortie de Visual Studio, qui doit contenir l’erreur complète.

## <a name="there-is-not-enough-storage-space-on-the-device-to-deploy-the-package"></a>Espace de stockage insuffisant sur l’appareil pour déployer le package

Cela se produit lorsque vous ne démarrez pas l’émulateur à partir de Visual Studio. Lors du démarrage de l’émulateur en dehors de Visual Studio, vous devez passer les options `-partition-size 512`, par exemple

```shell
emulator -partition-size 512 -avd MonoDroid
```

Veillez à utiliser le nom de simulateur correct, c.-à-d. [le nom que vous avez utilisé lors de la configuration du simulateur](~/android/get-started/installation/windows.md#device).

## <a name="install_failed_invalid_apk-when-installing-a-package"></a>L’installation de\_a échoué\_\_de APK non valide lors de l’installation d’un package

Les noms de packages Android *doivent* contenir un point (' *.* '). Modifiez le nom de votre package de sorte qu’il contienne un point.

- Dans Visual Studio :
  - Cliquez avec le bouton droit sur les propriétés de votre projet >
  - Cliquez sur l’onglet manifeste Android sur la gauche.
  - Mettez à jour le champ nom du package.
    - Si vous voyez le message &ldquo;aucun fichier AndroidManifest. xml n’a été trouvé. Cliquez pour en ajouter un.&rdquo;, cliquez sur le lien, puis mettez à jour le champ nom du package.
- Dans Visual Studio pour Mac :
  - Cliquez avec le bouton droit sur vos options de > de projet.
  - Accédez à la section Build/application Android.
  - Modifiez le champ nom du package pour qu’il contienne un « . ».

## <a name="install_failed_missing_shared_library-when-installing-a-package"></a>L’installation de\_a échoué\_bibliothèque de\_partagée\_MANQUante lors de l’installation d’un package

Dans ce contexte, une « bibliothèque partagée » n’est *pas* un fichier*libfoo.so*(Native Shared Library); au lieu de cela, il s’agit d’une bibliothèque qui doit être installée séparément sur l’appareil cible, par exemple Google Maps.

Le package Android spécifie les bibliothèques partagées qui sont requises avec l’élément `<uses-library/>`. Si une bibliothèque *requise* n’est pas présente sur l’appareil cible (par exemple, `//uses-library/@android:required` a la *valeur true*, qui est la valeur par défaut), l’installation du package échoue avec l’installation *\_a échoué\_\_bibliothèque de\_partagée manquante*.

Pour déterminer quelles bibliothèques partagées sont nécessaires, consultez le fichier *généré*
**fichier AndroidManifest. xml** (par exemple, **obj\\Debug\\Android\\fichier AndroidManifest. xml**) et recherchez les éléments de `<uses-library/>`. `<uses-library/>` éléments peuvent être ajoutés manuellement dans les propriétés de votre projet **\\fichier fichier AndroidManifest. xml** et via l' [attribut personnalisé UsesLibraryAttribute](xref:Android.App.UsesLibraryAttribute).

Par exemple, l’ajout d’une référence d’assembly à *mono. Android. GoogleMaps. dll* ajoutera implicitement un `<uses-library/>` pour la bibliothèque partagée Google Maps.

## <a name="install_failed_update_incompatible-when-installing-a-package"></a>ÉCHEC de l’installation\_\_mise à jour\_incompatible lors de l’installation d’un package

Les packages Android présentent trois exigences :

- Ils doivent contenir un'. ' (Voir l’entrée précédente)
- Ils doivent avoir un nom de package de chaîne unique (par conséquent, la Convention de type TLD inverse dans les noms d’application Android, par exemple, com. Android. chrome pour l’application chrome)
- Lors de la mise à niveau de packages, le package doit avoir la même clé de signature.

Imaginez donc ce scénario :

1. Vous générez & déployer votre application en tant qu’application de débogage
2. Vous modifiez la clé de signature, par exemple pour l’utiliser comme une application de mise en sortie (ou parce que vous n’aimez pas la clé de signature de débogage fournie par défaut)
3. Vous installez votre application sans la supprimer, par exemple, Déboguer > démarrer sans débogage dans Visual Studio

Dans ce cas, l’installation du package échouera avec une installation\_échec\_mettre à jour\_erreur incompatible, car le nom du package n’a pas changé pendant la signature de la clé de signature. Le [Journal de débogage Android](~/android/deploy-test/debugging/android-debug-log.md) contient également un message similaire à celui-ci :

```shell
E/PackageManager(  146): Package [PackageName] signatures do not match the previously installed version; ignoring!
```

Pour corriger cette erreur, supprimez complètement l’application de votre appareil avant de réinstaller.

## <a name="install_failed_uid_changed-when-installing-a-package"></a>ÉCHEC de l’installation\_\_UID\_modifié lors de l’installation d’un package

Lorsqu’un package Android est installé, il reçoit un *ID d’utilisateur* (UID).
*Parfois*, pour des raisons actuellement inconnues, lors de l’installation de sur une application déjà installée, l’installation échoue avec `INSTALL_FAILED_UID_CHANGED`:

```shell
ERROR [2015-03-23 11:19:01Z]: ANDROID: Deployment failed
Mono.AndroidTools.InstallFailedException: Failure [INSTALL_FAILED_UID_CHANGED]
   at Mono.AndroidTools.Internal.AdbOutputParsing.CheckInstallSuccess(String output, String packageName)
   at Mono.AndroidTools.AndroidDevice.<>c__DisplayClass2c.<InstallPackage>b__2b(Task`1 t)
   at System.Threading.Tasks.ContinuationTaskFromResultTask`1.InnerInvoke()
   at System.Threading.Tasks.Task.Execute()
```

Pour contourner ce problème, *désinstallez complètement* le package Android, soit en installant l’application à partir de l’interface graphique utilisateur de la cible Android, soit en utilisant `adb`:

```shell
$ adb uninstall @PACKAGE_NAME@
```

**N’utilisez pas** `adb uninstall -k`, car cela permet de *conserver* les données d’application et donc de conserver l’UID en conflit sur l’appareil cible.

## <a name="release-apps-fail-to-launch-on-device"></a>Échec du lancement des applications de version sur l’appareil

La sortie du journal de débogage Android contiendra-t-elle un message similaire à celui-ci :

```shell
D/AndroidRuntime( 1710): Shutting down VM
W/dalvikvm( 1710): threadid=1: thread exiting with uncaught exception (group=0xb412f180)
E/AndroidRuntime( 1710): FATAL EXCEPTION: main
E/AndroidRuntime( 1710): java.lang.UnsatisfiedLinkError: Couldn't load monodroid: findLibrary returned null
E/AndroidRuntime( 1710):        at java.lang.Runtime.loadLibrary(Runtime.java:365)
```

Dans ce cas, deux causes sont possibles :

1. Le. apk ne fournit pas d’ABI que l’appareil cible prend en charge.
    Par exemple, le. apk contient uniquement des binaires ARMEABI-V7A, et l’appareil cible prend en charge uniquement ARMEABI.

2. [Bogue Android](https://code.google.com/p/android/issues/detail?id=21670). Si c’est le cas, désinstallez l’application, franchissez vos doigts, puis réinstallez l’application.

Pour corriger (1), modifiez les options/propriétés du projet et [Ajoutez la prise en charge de l’Abi requis à la liste des Abi prises en charge](~/android/app-fundamentals/cpu-architectures.md). Pour déterminer quel ABI vous devez ajouter, exécutez la commande ADB suivante sur votre appareil cible :

```shell
adb shell getprop ro.product.cpu.abi
adb shell getprop ro.product.cpu.abi2
```

La sortie contient le Abi principal (et le secondaire facultatif).

```shell
$ adb shell getprop | grep ro.product.cpu
[ro.product.cpu.abi2]: [armeabi]
[ro.product.cpu.abi]: [armeabi-v7a]
```

## <a name="the-outpath-property-is-not-set-for-project-ldquomyappcsprojrdquo"></a>La propriété du chemin d’accès n’est pas définie pour le projet &ldquo;MyApp. csproj&rdquo;

Cela signifie généralement que vous avez un ordinateur HP et la variable d’environnement &ldquo;plateforme&rdquo; a été définie sur MCD ou HPD. Cela est en conflit avec la propriété de la plateforme MSBuild qui est généralement définie sur &ldquo;Any CPU&rdquo; ou &ldquo;&rdquo;x86. Vous devrez supprimer cette variable d’environnement de votre ordinateur avant que MSBuild puisse fonctionner :

- Panneau de configuration > les variables d’environnement > avancées du système >

Redémarrez Visual Studio ou Visual Studio pour Mac, puis régénérez. Les choses devraient maintenant fonctionner comme prévu.

## <a name="javalangclasscastexception-monoandroidruntimejavaobject-cannot-be-cast-to"></a>Java. lang. ClassCastException : mono. Android. Runtime. JavaObject ne peut pas être converti en...

Xamarin. Android 4. x ne marshale pas correctement les types génériques imbriqués correctement. Par exemple, considérez le code C\# suivant à l’aide de [SimpleExpandableListAdapter](xref:Android.Widget.SimpleExpandableListAdapter):

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

Le problème est que Xamarin. Android marshale incorrectement les types génériques imbriqués. Le `List<IDictionary<string, object>>` est marshalé en [java. lang. ArrrayList](xref:Java.Util.ArrayList), mais le `ArrayList` contient des instances `mono.android.runtime.JavaObject` (qui référencent les instances `Dictionary<string, object>`) au lieu d’un objet qui implémente [java. util. map](xref:Java.Util.IMap), provoquant l’exception suivante :

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

La solution de contournement consiste à utiliser les types de [collection Java](~/android/internals/api-design.md) fournis au lieu des types de `System.Collections.Generic` pour les types de&rdquo; internes &ldquo;. Cela entraînera des types Java appropriés lors du marshaling des instances. (Le code suivant est plus complexe que nécessaire afin de réduire les durées de vie des Gref. Vous pouvez simplifier la modification du code d’origine via `s/List/JavaList/g` et `s/Dictionary/JavaDictionary/g` si les durées de vie Gref ne vous inquiétent pas.)

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

[Ce problème sera résolu dans une version ultérieure](https://bugzilla.xamarin.com/show_bug.cgi?id=5401).

## <a name="unexpected-nullreferenceexceptions"></a>Exceptions NullReferenceException inattendu

Parfois, le [Journal de débogage Android](~/android/deploy-test/debugging/android-debug-log.md) mentionne exceptions nullreferenceexception que &ldquo;ne peut pas se produire,&rdquo; ou provient de mono pour le code d’exécution Android peu de temps avant que l’application ne meurt :

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

Cela peut se produire lorsque le runtime Android décide d’abandonner le processus, ce qui peut se produire pour plusieurs raisons, notamment pour atteindre la limite GREF de la cible ou pour faire quelque chose &ldquo;incorrecte&rdquo; avec JNI.

Pour voir si c’est le cas, recherchez dans le journal de débogage Android un message similaire à celui-ci :

```shell
E/dalvikvm(  123): VM aborting
```

## <a name="abort-due-to-global-reference-exhaustion"></a>Abandonner en raison de l’épuisement des références globales

La couche JNI du runtime Android ne prend en charge qu’un nombre limité de références d’objets JNI à un moment donné. Lorsque cette limite est dépassée, les choses s’arrêtent.

La limite GREF (*référence globale*) est 2000 références dans l’émulateur et ~ 52000 références sur le matériel.

Vous savez que vous commencez à créer un trop grand nombre d’GREFs lorsque vous voyez des messages tels que celui-ci dans le journal de débogage Android :

```shell
D/dalvikvm(  602): GREF has increased to 1801
```

Lorsque vous atteignez la limite GREF, un message tel que le suivant s’affiche :

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

Dans l’exemple ci-dessus (qui provient accidentellement du [bogue 685215](https://bugzilla.novell.com/show_bug.cgi?id=685215)) le problème est qu’un trop grand nombre d’instances Android. Graphics. point sont en cours de création ; pour obtenir la liste des correctifs pour ce bogue, consultez [\#de commentaires 2](https://bugzilla.novell.com/show_bug.cgi?id=685215#c2) .

En règle générale, une solution utile consiste à rechercher le type ayant trop d’instances allouées &ndash; Android. Graphics. point dans le dump ci-dessus &ndash; puis à rechercher où elles sont créées dans votre code source et à les supprimer de manière appropriée (afin que leur durée de vie d’objet Java soit raccourcie). Cela n’est pas toujours approprié (\#685215 est multithread, donc la solution triviale évite l’appel de suppression), mais c’est la première chose à prendre en compte.

Vous pouvez activer la [journalisation Gref](~/android/troubleshooting/index.md) pour voir à quel moment les GREFs sont créés et combien existent.

## <a name="abort-due-to-jni-type-mismatch"></a>Abandonner en raison d’une incompatibilité de type JNI

Si vous transmettez le code JNI manuellement, il est possible que les types ne correspondent pas correctement, par exemple si vous essayez d’appeler `java.lang.Runnable.run` sur un type qui n’implémente pas `java.lang.Runnable`. Dans ce cas, un message similaire à celui-ci s’affiche dans le journal de débogage Android :

```shell
W/dalvikvm( 123): JNI WARNING: can't call Ljava/Type;;.method on instance of Lanother/java/Type;
W/dalvikvm( 123):              in Lmono/java/lang/RunnableImplementor;.n_run:()V (CallVoidMethodA)
...
E/dalvikvm( 123): VM aborting
```

## <a name="dynamic-code-support"></a>Prise en charge du code dynamique

### <a name="dynamic-code-does-not-compile"></a>Le code dynamique ne se compile pas

Pour utiliser C\# dynamique dans votre application ou bibliothèque, vous devez ajouter System. Core. dll, Microsoft. CSharp. dll et mono. CSharp. dll à votre projet.

### <a name="in-release-build-missingmethodexception-occurs-for-dynamic-code-at-run-time"></a>Dans la version Release, MissingMethodException se produit pour le code dynamique au moment de l’exécution.

- Il est probable que votre projet d’application n’a pas de références à System. Core. dll, Microsoft. CSharp. dll ou mono. CSharp. dll. Assurez-vous que ces assemblys sont référencés.

  - Gardez à l’esprit que le code dynamique est toujours onéreux. Si vous avez besoin de code efficace, envisagez de ne pas utiliser de code dynamique.

- Dans le premier aperçu, ces assemblys ont été exclus, à moins que les types dans chaque assembly soient utilisés explicitement par le code d’application. Pour obtenir une solution de contournement, consultez les rubriques suivantes : [http://lists.ximian.com/pipermail/mo...il/009798.html](http://lists.ximian.com/pipermail/monodroid/2012-April/009798.html)

## <a name="projects-built-with-aotllvm-crash-on-x86-devices"></a>Projets générés avec l’AOA + LLVM Crash sur les appareils x86

Lors du déploiement d’une application générée avec [AOT + LLVM](~/android/deploy-test/release-prep/index.md) sur des appareils x86, un message d’erreur d’exception semblable au suivant peut s’afficher :

```shell
Assertion: should not be reached at /Users/.../external/mono/mono/mini/tramp-x86.c:124
Fatal signal 6 (SIGABRT), code -6 in tid 4051 (Xamarin.bug56111)
```

Il s’agit d’un problème connu : la solution de contournement consiste à désactiver LLVM.
