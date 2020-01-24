---
title: ProGuard
description: Xamarin.Android ProGuard est un réducteur, un optimiseur et un prévérificateur de fichiers de classe Java. Il détecte et supprime le code non utilisé, et analyse et optimise le bytecode. Ce guide explique le fonctionnement de ProGuard, comment l’activer dans votre projet et comment le configurer. Il fournit également plusieurs exemples de configuration de ProGuard.
ms.prod: xamarin
ms.assetid: 29C0E850-3A49-4618-9078-D59BE0284D5A
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: 1ae7850951060f2e89a953ce554a0dbfa286c3a1
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75487986"
---
# <a name="proguard"></a>ProGuard

_Xamarin. Android ProGuard est un compacteur de fichier de classe Java, un optimiseur et un pré-vérificateur. Il détecte et supprime le code inutilisé, analyse et optimise le bytecode. Ce guide explique le fonctionnement de ProGuard, comment l’activer dans votre projet et comment le configurer. Il fournit également plusieurs exemples de configurations ProGuard._

## <a name="overview"></a>Vue d'ensemble de

ProGuard détecte et supprime les classes, champs, méthodes et attributs non utilisés de votre application packagée. Il peut faire de même pour les bibliothèques référencées (ce qui peut vous éviter de dépasser la limite de 64K références). L’outil ProGuard du kit Android SDK optimise aussi le bytecode et supprime les instructions de code non utilisé. ProGuard lit les fichiers **JAR d’entrée**, puis les réduit, les optimise et les pré-vérifie. Il écrit les résultats dans un ou plusieurs fichiers **JAR de sortie**. 

ProGuard traite les APK d’entrée en effectuant les étapes suivantes : 

1. La réduction de l' **étape** &ndash; ProGuard détermine de manière récursive les classes et les membres de classe qui sont utilisés. Toutes les autres classes et membres de classe sont ignorés. 

2. **Étape d’optimisation** &ndash; ProGuard optimise davantage le code. 
    Ces optimisations peuvent prendre les formes suivantes : les classes et les méthodes qui ne sont pas des points d’entrée peuvent être rendus privées, statiques ou finales ; les paramètres non utilisés peuvent être supprimés ; et des méthodes peuvent être inline. 

3. **Étape d’obscurcissement** &ndash; dans le développement Android natif, ProGuard renomme les classes et les membres de classe qui ne sont pas des points d’entrée. Les points d’entrée sont conservés afin qu’ils soient toujours accessibles par leur nom d’origine. Toutefois, cette étape n’est pas prise en charge par Xamarin.Android, car l’application est compilée en langage intermédiaire (IL).

4. **Étape de prévérification** &ndash; effectue des vérifications sur les bytecodes Java avant le runtime et annote les fichiers de classe pour tirer parti de la machine virtuelle Java. Il s’agit de la seule étape qui n’a pas besoin de connaître les points d’entrée. 

Chacune de ces étapes est *facultative*. Comme expliqué dans la section suivante, l’outil ProGuard de Xamarin.Android utilise un sous-ensemble de ces étapes. 

## <a name="proguard-in-xamarinandroid"></a>ProGuard dans Xamarin.Android

La configuration ProGuard de Xamarin.Android n’obfusque pas l’APK. En fait, il n’est pas possible d’activer l’obfuscation via ProGuard (même via l’utilisation de fichiers de configuration personnalisés). Par conséquent, l’outil ProGuard de Xamarin.Android effectue uniquement les étapes de **réduction** et d’**optimisation** : 

[![Étapes de réduction et d’optimisation](proguard-images/01-xa-chain-sml.png)](proguard-images/01-xa-chain.png#lightbox)

Avant d’utiliser ProGuard, il est essentiel que vous sachiez comme il fonctionne dans le processus de génération de `Xamarin.Android`. Ce processus utilise deux étapes : 

1. Éditeur de liens Xamarin Android

2. ProGuard

Chacune de ces étapes est décrite ci-après.

### <a name="linker-step"></a>Étape Éditeur de liens

L’éditeur de liens Xamarin.Android utilise une analyse statique de votre application pour déterminer les points suivants : 

- Quels assemblys sont actuellement utilisés.

- Quels types sont actuellement utilisés.

- Quels membres sont actuellement utilisés. 

L’éditeur de liens est toujours exécuté avant l’étape ProGuard. Pour cette raison, l’éditeur de liens peut supprimer un assembly, un type ou un membre sur lequel vous vous attendiez à ce ProGuard s’exécute. (Pour plus d’informations sur la liaison dans Xamarin.Android, consultez [Liaison sur Android](~/android/deploy-test/linker.md).)

### <a name="proguard-step"></a>Étape ProGuard

Une fois l’étape Éditeur de liens terminée, ProGuard est exécuté afin de supprimer le bytecode Java non utilisé. Il s’agit de l’étape qui optimise l’APK. 

## <a name="using-proguard"></a>Utilisation de ProGuard

Pour utiliser ProGuard dans votre projet d’application, vous devez commencer par l’activer. Ensuite, vous pouvez laisser le processus de génération Xamarin.Android utiliser un fichier de configuration ProGuard par défaut ou vous pouvez créer votre propre fichier de configuration personnalisé. 

### <a name="enabling-proguard"></a>Activation de ProGuard

Utilisez les étapes suivantes pour activer ProGuard dans votre projet d’application :

1. Assurez-vous que votre projet est défini sur la configuration **Mise en production** (ce point est important car l’éditeur de liens doit être exécuté pour que ProGuard puisse s’exécuter) : 

    [![Sélectionner la configuration Mise en production](proguard-images/02-set-release-sml.png)](proguard-images/02-set-release.png#lightbox)
   
2. Choisissez **ProGuard** dans la liste déroulante de **code** pour les **Propriétés > fenêtre Options Android** : 

    [le Décompacteur de code ![ProGuard est sélectionné](proguard-images/03-enable-proguard-shrinker-sml.png)](proguard-images/03-enable-proguard-shrinker.png#lightbox)

Pour la plupart des applications Xamarin.Android, le fichier de configuration ProGuard par défaut fourni par Xamarin.Android sera suffisant pour supprimer tout le code non utilisé (et seulement lui). Pour afficher la configuration ProGuard par défaut, ouvrez le fichier **obj\\Release\\proguard\\proguard_xamarin.cfg**.

L’exemple suivant illustre un fichier **proguard_xamarin.cfg** généré typique :

```cfg
# This is Xamarin-specific (and enhanced) configuration.

-dontobfuscate

-keep class mono.MonoRuntimeProvider { *; <init>(...); }
-keep class mono.MonoPackageManager { *; <init>(...); }
-keep class mono.MonoPackageManager_Resources { *; <init>(...); }
-keep class mono.android.** { *; <init>(...); }
-keep class mono.java.** { *; <init>(...); }
-keep class mono.javax.** { *; <init>(...); }
-keep class opentk.platform.android.AndroidGameView { *; <init>(...); }
-keep class opentk.GameViewBase { *; <init>(...); }
-keep class opentk_1_0.platform.android.AndroidGameView { *; <init>(...); }
-keep class opentk_1_0.GameViewBase { *; <init>(...); }

-keep class android.runtime.** { <init>(***); }
-keep class assembly_mono_android.android.runtime.** { <init>(***); }
# hash for android.runtime and assembly_mono_android.android.runtime.
-keep class md52ce486a14f4bcd95899665e9d932190b.** { *; <init>(...); }
-keepclassmembers class md52ce486a14f4bcd95899665e9d932190b.** { *; <init>(...); }

# Android's template misses fluent setters...
-keepclassmembers class * extends android.view.View {
   *** set*(***);
}

# also misses those inflated custom layout stuff from xml...
-keepclassmembers class * extends android.view.View {
   <init>(android.content.Context,android.util.AttributeSet);
   <init>(android.content.Context,android.util.AttributeSet,int);
}
```

La section suivante décrit comment créer un fichier de configuration ProGuard personnalisé. 

### <a name="customizing-proguard"></a>Personnalisation de ProGuard

Si vous le souhaitez, vous pouvez ajouter un fichier de configuration ProGuard personnalisé pour exercer un plus grand contrôle sur les outils ProGuard. Par exemple, vous pouvez souhaiter indiquer explicitement à ProGuard quelles classes conserver. Pour ce faire, créez un nouveau fichier **cfg** et appliquez l’action de génération `ProGuardConfiguration` dans le volet **Propriétés** de l’**Explorateur de solutions** : 

[![Action de génération ProguardConfiguration sélectionnée](proguard-images/04-build-action-sml.png)](proguard-images/04-build-action.png#lightbox)

Gardez à l’esprit que ce fichier de configuration ne remplace pas le fichier **proguard_xamarin.cfg** de Xamarin.Android, car les deux sont utilisés par ProGuard. 

Il peut arriver que ProGuard ne parvienne pas à analyser correctement votre application et supprime du code dont votre application a en fait besoin. Si cela se produit, vous pouvez ajouter une ligne `-keep` à votre fichier de configuration ProGuard personnalisé : 

```
-keep public class MyClass
```

Dans cet exemple, `MyClass` est défini sur le nom réel de la classe que vous souhaitez que ProGuard ignore.

Vous pouvez également enregistrer vos propres noms avec des annotations `[Register]` et utiliser ces noms pour personnaliser les règles ProGuard. Vous pouvez enregistrer des noms pour les classes Adapter, View, BroadcastReceiver, Service, ContentProvider, Activity et Fragment. Pour plus d’informations sur l’utilisation de l’attribut de personnalisation `[Register]`, consultez [Utilisation de JNI](~/android/platform/java-integration/working-with-jni.md).

### <a name="proguard-options"></a>Options de ProGuard

ProGuard propose différentes options que vous pouvez configurer pour un contrôle plus précis de son fonctionnement. Le [Guide ProGuard](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/index.html#manual/introduction.html) fournit des informations de référence complètes sur l’utilisation de ProGuard. 

Xamarin.Android prend en charge les options suivantes de ProGuard : 

- [Options d’entrée/sortie](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#iooptions)

- [Options de conservation](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#keepoptions)

- [Options de réduction](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#shrinkingoptions)

- [Options générales](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#generaloptions)

- [Chemins de classes](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#classpath)

- [Noms de fichiers](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#filename)

- [Filtres de fichiers](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#filefilters)

- [Les filtres](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#filters)

- [Vue d’ensemble des options `Keep`](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#keepoverview)

- [Modificateurs de l’option Keep](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#keepoptionmodifiers)

- [Spécifications de classes](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#classspecification)

Les options suivantes sont *ignorées* par Xamarin.Android :

- [Options d’optimisation](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#optimizationoptions)

- [Options d’obfuscation](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#obfuscationoptions) 

- [Options de prévérification](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#preverificationoptions)

## <a name="proguard-and-android-nougat"></a>ProGuard et Android Nougat

Si vous essayez d’utiliser ProGuard sur Android 7.0 ou version supérieure, vous devez télécharger une version plus récente de ProGuard car le kit Android SDK ne fournit pas une nouvelle version compatible avec JDK 1.8.

Vous pouvez utiliser ce [package NuGet](https://www.nuget.org/packages/name.atsushieno.proguard.facebook/5.3.0) pour installer une version plus récente de `proguard.jar`. Pour plus d’informations sur la mise à jour de la version par défaut de `proguard.jar` du kit Android SDK, consultez cette discussion sur [Stack Overflow](https://stackoverflow.com/questions/39514518/xamarin-android-proguard-unsupported-class-version-number-52-0/39514706#39514706).

Toutes les versions de ProGuard sont disponibles sur la page [SourceForge](https://sourceforge.net/projects/proguard/files/). 

## <a name="example-proguard-configurations"></a>Exemples de configuration de ProGuard

Deux exemples de fichiers de configuration ProGuard sont fournis ci-dessous. Notez que le processus de génération Xamarin.Android fournira les fichiers JAR d’**entrée**, de **sortie** et de **bibliothèque**. Par conséquent, vous pouvez vous concentrer sur les autres options, comme `-keep`. 

### <a name="a-simple-android-activity"></a>Activité Android simple

L’exemple suivant illustre la configuration d’une activité Android simple :

```
-injars  bin/classes
-outjars bin/classes-processed.jar
-libraryjars /usr/local/java/android-sdk/platforms/android-9/android.jar

-dontpreverify
-repackageclasses ''
-allowaccessmodification
-optimizations !code/simplification/arithmetic

-keep public class mypackage.MyActivity
```

### <a name="a-complete-android-application"></a>Application Android complète

L’exemple suivant illustre la configuration d’une application Android complète :

```
-injars  bin/classes
-injars  libs
-outjars bin/classes-processed.jar
-libraryjars /usr/local/java/android-sdk/platforms/android-9/android.jar

-dontpreverify
-repackageclasses ''
-allowaccessmodification
-optimizations !code/simplification/arithmetic
-keepattributes *Annotation*

-keep public class * extends android.app.Activity
-keep public class * extends android.app.Application
-keep public class * extends android.app.Service
-keep public class * extends android.content.BroadcastReceiver
-keep public class * extends android.content.ContentProvider

-keep public class * extends android.view.View {
public <init>(android.content.Context);
public <init>(android.content.Context, android.util.AttributeSet);
public <init>(android.content.Context, android.util.AttributeSet, int);
public void set*(...);
}

-keepclasseswithmembers class * {
public <init>(android.content.Context, android.util.AttributeSet);
}

-keepclasseswithmembers class * {
public <init>(android.content.Context, android.util.AttributeSet, int);
}

-keepclassmembers class * implements android.os.Parcelable {
static android.os.Parcelable$Creator CREATOR;
}

-keepclassmembers class **.R$* {
public static <fields>;
}
```

## <a name="proguard-and-the-xamarinandroid-build-process"></a>ProGuard et le processus de génération Xamarin.Android

Les sections suivantes expliquent comment ProGuard s’exécute pendant le processus de génération de build de **mise en production** de Xamarin.Android.

### <a name="what-command-is-proguard-running"></a>Quelles commande ProGuard exécute-t-il ?

ProGuard est simplement un fichier `.jar` fourni avec le kit Android SDK. Par conséquent, il est appelé dans une commande : 

```shell
java -jar proguard.jar options ...
```

### <a name="the-proguard-task"></a>La tâche ProGuard

La tâche ProGuard se trouve dans l’assembly **Xamarin.Android.Build.Tasks.dll**. Elle fait partie du `_CompileToDalvikWithDx` cible, qui est une partie du `_CompileDex` cible. 

La liste suivante est un exemple des paramètres par défaut qui sont générés après que vous avez créé un projet à l’aide de **Fichier > Nouveau projet** : 

```
ProGuardJarPath = C:\Android\android-sdk\tools\proguard\lib\proguard.jar
AndroidSdkDirectory = C:\Android\android-sdk\
JavaToolPath = C:\Program Files (x86)\Java\jdk1.8.0_92\\bin
ProGuardToolPath = C:\Android\android-sdk\tools\proguard\
JavaPlatformJarPath = C:\Android\android-sdk\platforms\android-25\android.jar
ClassesOutputDirectory = obj\Release\android\bin\classes
AcwMapFile = obj\Release\acw-map.txt
ProGuardCommonXamarinConfiguration = obj\Release\proguard\proguard_xamarin.cfg
ProGuardGeneratedReferenceConfiguration = obj\Release\proguard\proguard_project_references.cfg
ProGuardGeneratedApplicationConfiguration = obj\Release\proguard\proguard_project_primary.cfg
ProGuardConfigurationFiles

    {sdk.dir}tools\proguard\proguard-android.txt;
    {intermediate.common.xamarin};
    {intermediate.references};
    {intermediate.application};
    ;

JavaLibrariesToEmbed = C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\MonoAndroid\v7.0\mono.android.jar
ProGuardJarInput = obj\Release\proguard\__proguard_input__.jar
ProGuardJarOutput = obj\Release\proguard\__proguard_output__.jar
DumpOutput = obj\Release\proguard\dump.txt
PrintSeedsOutput = obj\Release\proguard\seeds.txt
PrintUsageOutput = obj\Release\proguard\usage.txt
PrintMappingOutput = obj\Release\proguard\mapping.txt
```

L’exemple suivant illustre une commande ProGuard classique qui est exécutée à partir de l’IDE :

```cmd
C:\Program Files (x86)\Java\jdk1.8.0_92\\bin\java.exe -jar C:\Android\android-sdk\tools\proguard\lib\proguard.jar -include obj\Release\proguard\proguard_xamarin.cfg -include obj\Release\proguard\proguard_project_references.cfg -include obj\Release\proguard\proguard_project_primary.cfg "-injars 'obj\Release\proguard\__proguard_input__.jar';'C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\MonoAndroid\v7.0\mono.android.jar'" "-libraryjars 'C:\Android\android-sdk\platforms\android-25\android.jar'" -outjars "obj\Release\proguard\__proguard_output__.jar" -optimizations !code/allocation/variable
```

## <a name="troubleshooting"></a>Résolution des problèmes

### <a name="file-issues"></a>Problèmes de fichier

Le message d’erreur suivant peut s’afficher lorsque ProGuard lit son fichier de configuration : 

```
Unknown option '-keep' in line 1 of file 'proguard.cfg'
```

Ce problème se produit en général sur Windows, car le fichier `.cfg` utilise un codage incorrect. ProGuard ne peut pas gérer la _marque d’ordre d’octet_ (BOM, byte order mark) susceptible d’être présente dans les fichiers texte. En présence d’une marque d’ordre d’octet, ProGuard quitte avec l’erreur ci-dessus. 

<!-- markdownlint-disable MD001 -->

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Pour éviter ce problème, modifiez le fichier de configuration personnalisé à partir d’un éditeur de texte qui autorise l’enregistrement du fichier sans marque d’ordre d’octet. Pour résoudre ce problème, assurez-vous que l’encodage de votre éditeur de texte est défini sur `UTF-8`. Par exemple, l’éditeur de texte [Bloc-notes ++](https://notepad-plus-plus.org/) peut enregistrer des fichiers sans marque d’ordre d’octet en sélectionnant **Encodage &gt; Encoder en UTF-8 (sans BOM)** lors de l’enregistrement du fichier. 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

Pour éviter ce problème, enregistrez votre fichier de configuration personnalisé à partir d’un éditeur de texte qui vous permet d’omettre la marque d’ordre d’octet. 

-----

### <a name="other-issues"></a>Autres problèmes

La page [Dépannage](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/index.html#manual/troubleshooting.html) de ProGuard traite des problèmes courants que vous pouvez rencontrer lors de l’utilisation de ProGuard (et de leurs solutions).

## <a name="summary"></a>Récapitulatif

Dans ce guide, vous avez appris comment fonctionnait ProGuard dans Xamarin.Android, comment l’activer dans votre projet d’application et comment le configurer. Vous avez eu accès à des exemples de configuration de ProGuard et avez eu connaissance des solutions aux problèmes courants. Pour plus d’informations sur l’outil ProGuard et Android, consultez [Réduire votre code et vos ressources](https://developer.android.com/tools/help/proguard.html). 

## <a name="related-links"></a>Liens associés

- [Préparation d’une application pour la mise en production](~/android/deploy-test/release-prep/index.md)
