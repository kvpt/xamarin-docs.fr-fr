---
title: Processus de génération
ms.prod: xamarin
ms.assetid: 3BE5EE1E-3FF6-4E95-7C9F-7B443EE3E94C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/06/2020
ms.openlocfilehash: bce2b6f29129894ed446100c87b5e92d3572ed2f
ms.sourcegitcommit: 60d2243809d8e980fca90b9f771e72f8c0e64d71
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2020
ms.locfileid: "78946270"
---
# <a name="build-process"></a>Processus de génération

## <a name="overview"></a>Vue d’ensemble

Le processus de génération de Xamarin.Android est chargé de tout rassembler : [génération de `Resource.designer.cs`](~/android/internals/api-design.md), prise en charge de `AndroidAsset`, `AndroidResource` et d’autres [actions de génération](#Build_Actions), génération de [wrappers appelables par Android](~/android/platform/java-integration/android-callable-wrappers.md) et génération d’un fichier `.apk` pour l’exécution sur les appareils Android.

## <a name="application-packages"></a>Packages d’application

En gros, il existe deux types de packages d’application Android (les fichiers `.apk`) que le système de génération Xamarin.Android peut produire :

- Des versions **Release**, qui sont entièrement autonomes et ne nécessitent pas de packages supplémentaires pour s’exécuter. Ce sont ces packages qui sont fournis à un App Store.

- Des versions **Debug**, qui ne le sont pas.

Ce n’est pas par hasard qu’elles correspondent à la `Configuration` de MSBuild qui produit le package.

### <a name="shared-runtime"></a>Runtime partagé

Le *runtime partagé* est une paire de packages Android supplémentaires qui fournissent la bibliothèque de classes de base (`mscorlib.dll`, etc.) et la bibliothèque de liaison Android (`Mono.Android.dll`, etc.). Les versions Debug s’appuient sur le runtime partagé au lieu d’inclure la bibliothèque de classes de base et les assemblys de liaison dans le package d’application Android, ce qui explique la taille plus petite du package Debug.

Le runtime partagé peut être désactivé dans les versions Debug en définissant la propriété `$(AndroidUseSharedRuntime)` sur `False`.

<a name="Fast_Deployment" />

### <a name="fast-deployment"></a>Déploiement rapide

Le *déploiement rapide* fonctionne conjointement avec le runtime partagé pour réduire encore plus la taille du package d’application Android. Pour cela, les assemblys de l’application ne sont pas intégrés dans le package. Ils sont plutôt copiés sur la cible via `adb push`. Ce processus accélère le cycle de génération/déploiement/débogage, car si *seuls* des assemblys sont modifiés, le package n’est pas réinstallé. Au lieu de cela, seuls les assemblys mis à jour sont resynchronisées sur l’appareil cible.

Le déploiement rapide est connu pour échouer sur les appareils qui bloquent la synchronisation de `adb` pour le répertoire `/data/data/@PACKAGE_NAME@/files/.__override__`.

Le déploiement rapide est activé par défaut et peut être désactivé dans les versions Debug en définissant la propriété `$(EmbedAssembliesIntoApk)` sur `True`.

## <a name="msbuild-projects"></a>Projets MSBuild

Le processus de génération de Xamarin.Android est basé sur MSBuild, qui est également le format de fichier projet utilisé par Visual Studio pour Mac et Visual Studio.
En règle générale, les utilisateurs n’ont pas besoin de modifier les fichiers MSBuild manuellement, car l’IDE crée des projets entièrement fonctionnels et les met à jour avec toutes les modifications apportées, puis appelle automatiquement les cibles de génération en fonction des besoins.

Les utilisateurs avancés peuvent néanmoins souhaiter effectuer des opérations non prises en charge par l’interface graphique utilisateur de l’IDE : le processus de génération est donc personnalisable en modifiant le fichier projet directement.
Cette page décrit seulement les fonctionnalités et les personnalisations spécifiques à Xamarin.Android : bien d’autres choses sont possibles avec les éléments, les propriétés et les cibles normales de MSBuild.

<a name="Build_Targets" />

## <a name="build-targets"></a>Cibles de génération

Les cibles de génération suivantes sont définies pour les projets Xamarin.Android :

- **Build** &ndash; génère le package.

- **BuildAndStartAotProfiling** &ndash; génère l’application avec un profileur AOA incorporé, définit le port TCP du profileur sur `$(AndroidAotProfilerPort)`et démarre l’activité par défaut.

  Le port TCP par défaut est `9999`.

  Ajouté dans Xamarin. Android 10,2.

- **Clean** &ndash; supprime tous les fichiers générés par le processus de génération.

- **FinishAotProfiling** &ndash; collecte les données du profileur AOA à partir de l’appareil ou de l’émulateur via le port TCP `$(AndroidAotProfilerPort)` et les écrit dans `$(AndroidAotCustomProfilePath)`.

  Les valeurs par défaut pour les profils de port et personnalisé sont `9999` et `custom.aprof`.

  Pour passer des options supplémentaires à `aprofutil`, définissez-les dans la propriété `$(AProfUtilExtraOptions)`.

  Ceci équivaut à :

  ```
  aprofutil $(AProfUtilExtraOptions) -s -v -f -p $(AndroidAotProfilerPort) -o "$(AndroidAotCustomProfilePath)"
  ```

  Ajouté dans Xamarin. Android 10,2.

- **Installer** &ndash; installe le package sur le périphérique par défaut ou l’appareil virtuel.

- **SignAndroidPackage** &ndash; crée et signe le package (`.apk`). À utiliser avec `/p:Configuration=Release` pour générer des packages « Release » autonomes.

- **StartAndroidActivity** &ndash; démarre l’activité par défaut sur l’appareil ou l’émulateur en cours d’exécution. Pour démarrer une autre activité, définissez la propriété `$(AndroidLaunchActivity)` sur le nom de l’activité.

  Ceci équivaut à `adb shell am start @PACKAGE_NAME@/$(AndroidLaunchActivity)`.

  Ajouté dans Xamarin. Android 10,2.

- **StopAndroidPackage** &ndash; arrête complètement le package d’application sur l’appareil ou l’émulateur en cours d’exécution.

  Ceci équivaut à `adb shell am force-stop @PACKAGE_NAME@`.

  Ajouté dans Xamarin. Android 10,2.

- **Désinstaller** &ndash; désinstalle le package du périphérique par défaut ou de l’appareil virtuel.

- **UpdateAndroidResources** &ndash; met à jour le fichier `Resource.designer.cs`. Cette cible est généralement appelée par l’IDE quand de nouvelles ressources sont ajoutées au projet.

## <a name="build-extension-points"></a>Créer des points d’extension

Le système de génération Xamarin.Android expose quelques points d’extension publics pour les utilisateurs souhaitant utiliser notre processus de génération. Pour utiliser l’un de ces points d’extension, vous devez ajouter votre cible personnalisée à la propriété MSBuild appropriée dans un `PropertyGroup`. Par exemple :

```xml
<PropertyGroup>
   <AfterGenerateAndroidManifest>
      $(AfterGenerateAndroidManifest);
      YourTarget;
   </AfterGenerateAndroidManifest>
</PropertyGroup>
```

Un mot de prudence pour étendre le processus de génération : s’il n’est pas écrit correctement, les extensions de build peuvent affecter les performances de votre Build, en particulier si elles s’exécutent à chaque Build. Il est vivement recommandé de lire la [documentation](https://docs.microsoft.com/visualstudio/msbuild/msbuild) MSBuild avant d’implémenter ces extensions.

- **AfterGenerateAndroidManifest** &ndash; cibles listées dans cette propriété seront exécutées directement après la cible de `_GenerateJavaStubs` interne. C’est là que le fichier `AndroidManifest.xml` est généré dans `$(IntermediateOutputPath)`. Par conséquent, si vous souhaitez apporter des modifications au fichier `AndroidManifest.xml` généré, vous pouvez le faire à l’aide de ce point d’extension.

  Ajouté dans Xamarin.Android 9.4.

- **BeforeGenerateAndroidManifest** &ndash; cibles listées dans cette propriété seront exécutées directement avant `_GenerateJavaStubs`.

  Ajouté dans Xamarin.Android 9.4.

## <a name="build-properties"></a>Propriétés de build

Les propriétés MSBuild contrôlent le comportement des cibles. Elles sont spécifiées dans le fichier projet, par exemple **MyApp.csproj**, dans un [élément MSBuild PropertyGroup](https://docs.microsoft.com/visualstudio/msbuild/propertygroup-element-msbuild).

- **Configuration** &ndash; spécifie la configuration de build à utiliser, par exemple « Debug » ou « Release ». La propriété Configuration est utilisée pour déterminer les valeurs par défaut pour d’autres propriétés qui déterminent le comportement de la cible. Vous pouvez créer des configurations supplémentaires dans votre IDE.

  *Par défaut*, la configuration `Debug` fait que les cibles `Install` et `SignAndroidPackage` créent un package Android plus petit qui nécessite la présence d’autres fichiers et packages pour fonctionner.

  Dans la configuration `Release` par défaut, les cibles `Install` et `SignAndroidPackage` créent un package Android *autonome* et utilisable sans installer d’autres packages ou fichiers.

- **DebugSymbols** &ndash; une valeur booléenne qui détermine si le package Android est pouvant être *débogué*, en association avec la propriété `$(DebugType)`. Un package débogable contient des symboles de débogage, définit l’attribut `//application/@android:debuggable` sur `true` et ajoute automatiquement l’autorisation `INTERNET` de façon à ce qu’un débogueur puisse s’attacher au processus. Une application peut être déboguée si `DebugSymbols` est `True` *et* `DebugType` est une chaîne vide ou une `Full`.

- **DebugType** &ndash; spécifie le [type de symboles de débogage](https://docs.microsoft.com/visualstudio/msbuild/csc-task) à générer dans le cadre de la génération, ce qui a également un impact sur la possibilité de déboguer l’application. Les valeurs possibles incluent :

  - **Full** : des symboles complets sont générés. Si la propriété MSBuild `DebugSymbols` est également définie sur `True`, le package d’application est débogable.

  - **PdbOnly**: les symboles « PDB » sont générés. Le package d’application ne sera *pas* débogable.

  Si `DebugType` n’est pas défini ou est une chaîne vide, la propriété `DebugSymbols` contrôle si l’application est ou non débogable.

  - **AndroidGenerateLayoutBindings** &ndash; permet la génération de [code-behind de disposition](https://github.com/xamarin/xamarin-android/blob/master/Documentation/guides/LayoutCodeBehind.md) si elle est définie sur `true` ou la désactive complètement si elle est définie sur `false`. La valeur par défaut est `false`.

### <a name="install-properties"></a>Propriétés d’installation

Les propriétés d’installation contrôlent le comportement des cibles `Install` et `Uninstall`.

- **AdbTarget** &ndash; spécifie l’appareil cible Android sur lequel le package Android peut être installé ou supprimé. La valeur de cette propriété est la même que celle de l’option de l’appareil cible[`adb`](https://developer.android.com/tools/help/adb.html#issuingcommands) :

  ```bash
  # Install package onto emulator via -e
  # Use `/Library/Frameworks/Mono.framework/Commands/msbuild` on OS X
  MSBuild /t:Install ProjectName.csproj /p:AdbTarget=-e
  ```

### <a name="packaging-properties"></a>Propriétés de packaging

Les propriétés de packaging contrôlent la création du package Android et sont utilisées par les cibles `Install` et `SignAndroidPackage`.
Les [propriétés de signature](#Signing_Properties) sont également impliquées pour le packaging des applications d’une version.

- **AndroidAotProfiles** &ndash; une propriété de type chaîne qui permet au développeur d’ajouter des profils AOA à partir de la ligne de commande. Il s’agit d’une liste de chemins absolus séparés par des points-virgules ou des virgules.

  Ajouté dans Xamarin. Android 10,1.

- **AndroidApkDigestAlgorithm** &ndash; une valeur de chaîne qui spécifie l’algorithme Digest à utiliser avec `jarsigner -digestalg`.

  La valeur par défaut est `SHA-256`. Dans Xamarin. Android 10,0 et versions antérieures, la valeur par défaut était `SHA1`.

  Ajouté dans Xamarin.Android 9.4.

- **AndroidApkSignerAdditionalArguments** &ndash; une propriété de type chaîne qui permet au développeur de fournir des arguments supplémentaires à l’outil `apksigner`.

  Ajouté dans Xamarin.Android 8.2.

- **AndroidApkSigningAlgorithm** &ndash; une valeur de chaîne qui spécifie l’algorithme de signature à utiliser avec `jarsigner -sigalg`.

  La valeur par défaut est `SHA256withRSA`. Dans Xamarin. Android 10,0 et versions antérieures, la valeur par défaut était `md5withRSA`.

  Ajouté dans Xamarin.Android 8.2.

- **AndroidApplication** &ndash; une valeur booléenne qui indique si le projet est destiné à une application android (`True`) ou à un projet de bibliothèque android (`False` ou absent).

  Un seul projet avec `<AndroidApplication>True</AndroidApplication>` peut être présent dans un package Android. (Malheureusement, ceci n’a pas encore été implémenté, ce qui peut entraîner des erreurs subtiles et inattendues concernant des ressources Android.)

- **AndroidApplicationJavaClass** &ndash; le nom complet de la classe Java à utiliser à la place de `android.app.Application` quand une classe hérite d' [Android. app. application](xref:Android.App.Application).

  Cette propriété est généralement définie par *d’autres* propriétés, comme la propriété MSBuild `$(AndroidEnableMultiDex)`.

  Ajouté dans Xamarin.Android 6.1.

- **AndroidBinUtilsPath** &ndash; un chemin d’accès à un répertoire contenant les [binutils][binutils] Android comme `ld`, l’éditeur de liens natif et `as`, l’assembleur natif. Ces outils font partie du NDK Android et sont également inclus dans l’installation de Xamarin. Android.

  La valeur par défaut est `$(MonoAndroidBinDirectory)\ndk\`.

  Ajouté dans Xamarin. Android 10,0.

  [binutils]: https://android.googlesource.com/toolchain/binutils/

- **AndroidBoundExceptionType** &ndash; une valeur de chaîne qui spécifie comment les exceptions doivent être propagées lorsqu’un type fourni par Xamarin. Android implémente un type ou une interface .net en termes de types Java, par exemple `Android.Runtime.InputStreamInvoker` et `System.IO.Stream`, ou `Android.Runtime.JavaDictionary` et `System.Collections.IDictionary`.

  - `Java`: le type d’exception Java d’origine est propagé tel quel.

    Cela signifie que, par exemple, `InputStreamInvoker` n’implémente pas correctement l’API `System.IO.Stream`, car `Java.IO.IOException` peut être levée à partir de `Stream.Read()` au lieu de `System.IO.IOException`.

    Il s’agit du comportement de propagation des exceptions dans toutes les versions de Xamarin. Android antérieures à 10,2.

    Il s’agit de la valeur par défaut dans Xamarin. Android 10,2.

  - `System`: le type d’exception Java d’origine est intercepté et encapsulé dans un type d’exception .NET approprié.

    Cela signifie que, par exemple, `InputStreamInvoker` implémente correctement `System.IO.Stream`et `Stream.Read()` ne lèvera *pas* d’instances `Java.IO.IOException`.  (Au lieu de cela, il peut lever une `System.IO.IOException` qui a un `Java.IO.IOException` comme valeur `Exception.InnerException`.)

    Celle-ci devient la valeur par défaut dans Xamarin. Android 11,0.

  Ajouté dans Xamarin. Android 10,2.

- **AndroidBuildApplicationPackage** &ndash; une valeur booléenne qui indique s’il faut créer et signer le package (. apk). Définir cette valeur sur `True` équivaut à utiliser la cible de build [SignAndroidPackage](#Build_Targets).

  La prise en charge de cette propriété a été ajoutée après Xamarin.Android 7.1.

  Cette propriété est définie par défaut sur `False`.

- **AndroidBundleConfigurationFile** &ndash; spécifie un nom de fichier à utiliser comme [fichier de configuration][bundle-config-format] pour `bundletool` lors de la génération d’un bundle d’applications Android. Ce fichier contrôle certains aspects de la façon dont les fichiers APK sont générés à partir du regroupement, comme les dimensions que le bundle fractionne pour produire des fichiers APK. Notez que Xamarin. Android configure automatiquement certains de ces paramètres, y compris la liste des extensions de fichier à conserver non compressées.

  Cette propriété est pertinente uniquement si `$(AndroidPackageFormat)` est défini sur `aab`.

  Ajouté dans Xamarin. Android 10,3.

  [bundle-config-format]: https://developer.android.com/studio/build/building-cmdline#bundleconfig

- **AndroidDexTool** &ndash; une propriété enum-style avec des valeurs valides de `dx` ou `d8`. Indique le compilateur [dex][dex] Android utilisé pendant le processus de génération de Xamarin.Android.
  La valeur par défaut est `dx`. Pour plus d’informations, consultez notre documentation sur [D8 et R8][d8-r8].

  [dex]: https://source.android.com/devices/tech/dalvik/dalvik-bytecode
  [d8-r8]: https://github.com/xamarin/xamarin-android/blob/master/Documentation/guides/D8andR8.md

- **AndroidEnableDesugar** &ndash; propriété booléenne qui détermine si `desugar` est activé. Android ne prend pas en charge toutes les fonctionnalités Java 8, et la chaîne d’outils par défaut implémente les nouvelles fonctionnalités de langage en effectuant des transformations de bytecode, appelées `desugar`, sur la sortie du compilateur `javac`. La valeur par défaut est `False` si vous utilisez `AndroidDexTool=dx`, ou `True` si vous utilisez `AndroidDexTool=d8`.

- **AndroidEnableGooglePlayStoreChecks** &ndash; une propriété bool qui permet aux développeurs de désactiver les vérifications de Google Play Store suivantes : XA1004, XA1005 et XA1006. Elle est utile pour les développeurs qui ne ciblent pas le Google Play Store et ne souhaitent pas exécuter ces vérifications.

  Ajouté dans Xamarin.Android 9.4.

- **AndroidEnableMultiDex** &ndash; propriété booléenne qui détermine si la prise en charge de multi-DEX sera utilisée dans le `.apk`final.

  La prise en charge de cette propriété a été ajoutée dans Xamarin.Android 5.1.

  Cette propriété est définie par défaut sur `False`.

- **AndroidEnablePreloadAssemblies** &ndash; propriété booléenne qui contrôle si tous les assemblys managés regroupés dans le package d’application sont chargés au démarrage du processus ou non.

  Avec la valeur `True`, tous les assemblys fournis dans le package d’application seront chargés lors du démarrage du processus, avant l’appel de tout code d’application.
  Ce processus est similaire à ce que faisait Xamarin.Android dans les versions antérieures à Xamarin.Android 9.2.

  Avec la valeur `False`, les assemblys seront uniquement chargés selon les besoins.
  Ce processus permet aux applications de démarrer plus rapidement et correspond davantage à la sémantique .NET.  Pour visualiser le gain de temps obtenu, définissez la propriété système `debug.mono.log` pour inclure `timing`, puis recherchez le message `Finished loading assemblies: preloaded` dans `adb logcat`.

  Pour les applications ou bibliothèques qui utilisent l’injection de dépendances, cette propriété *devra* éventuellement être définie sur `True` si elles nécessitent en retour que `AppDomain.CurrentDomain.GetAssemblies()` renvoie tous les assemblys dans le bundle d’application, même si l’assembly n’était pas nécessaire.

  Par défaut, cette valeur sera définie sur `True`.

  Ajouté dans Xamarin.Android 9.2.

- **AndroidEnableProfiledAot** &ndash; propriété booléenne qui détermine si les profils AOA sont utilisés lors de la compilation anticipée.

  Les profils sont listés dans le groupe d’éléments `AndroidAotProfile`. Ce groupe d’éléments contient le ou les profils par défaut. Vous pouvez les remplacer en supprimant les profils existants et en ajoutant vos propres profils AOT.

  La prise en charge de cette propriété a été ajoutée dans Xamarin.Android 9.4.

  Cette propriété est définie par défaut sur `False`.

- **AndroidEnableSGenConcurrent** &ndash; propriété booléenne qui détermine si le [collecteur gc simultané](https://www.mono-project.com/docs/about-mono/releases/4.8.0/#concurrent-sgen) de mono sera utilisé ou non.

  La prise en charge de cette propriété a été ajoutée dans Xamarin.Android 7.2.

  Cette propriété est définie par défaut sur `False`.

- **AndroidErrorOnCustomJavaObject** &ndash; propriété booléenne qui détermine si les types peuvent implémenter `Android.Runtime.IJavaObject`
  *sans* hériter également de `Java.Lang.Object` ou `Java.Lang.Throwable`:

  ```csharp
  class BadType : IJavaObject {
      public IntPtr Handle {
          get {return IntPtr.Zero;}
      }

      public void Dispose()
      {
      }
  }
  ```

  Lorsque sa valeur est True, ces types génèrent une erreur XA4212 ; sinon, un avertissement XA4212 est généré.

  Ajouté dans Xamarin.Android 8.1.

  Cette propriété est définie par défaut sur `True`.

- **AndroidExtraAotOptions** &ndash; une propriété de type chaîne qui permet de passer des options supplémentaires au compilateur mono au cours de la tâche `Aot` pour les projets qui ont soit `$(AndroidEnableProfiledAot)`, soit `$(AotAssemblies)` défini sur `true`. La valeur de chaîne de la propriété est ajoutée au fichier réponse lors de l’appel du compilateur croisé mono.

  En général, cette propriété doit être laissée vide, mais dans certains scénarios spéciaux, elle peut fournir une flexibilité utile.

  Notez que cette propriété est différente de la propriété `$(AndroidAotAdditionalArguments)` associée. Cette propriété place les arguments séparés par des virgules dans l’option `--aot` du compilateur mono. `$(AndroidExtraAotOptions)` passe à la place des options autonomes, séparées par des espaces, comme `--verbose` ou `--debug` au compilateur.

  Ajouté dans Xamarin. Android 10,2.

- **AndroidFastDeploymentType** &ndash; une liste de valeurs séparées par des `:` (deux-points) pour contrôler les types qui peuvent être déployés dans le [Répertoire de déploiement rapide](#Fast_Deployment) sur l’appareil cible lorsque la propriété MSBuild `$(EmbedAssembliesIntoApk)` est `False`. Si une ressource est déployée via le déploiement rapide, elle n’est *pas* incorporée dans le fichier `.apk` généré, ce qui peut accélérer les temps de déploiement. (Plus il s’agit d’un déploiement rapide, moins le `.apk` doit être régénéré, et le processus d’installation peut être plus rapide.) Les valeurs valides sont les suivantes :

  - `Assemblies` : déployer les assemblys de l’application.

  - `Dexes` : déployer les fichiers `.dex`, les ressources Android et les composants Android. **Cette valeur peut être utilisée *uniquement* sur les appareils exécutant Android 4.4 ou ultérieur (API-19).**

  La valeur par défaut est `Assemblies`.

  **Experimental**. Ajouté dans Xamarin.Android 6.1.

- **AndroidGenerateJniMarshalMethods** &ndash; une propriété bool qui permet la génération de méthodes marshals JNI dans le cadre du processus de génération. Cela réduit considérablement l’utilisation de System.Reflection dans le code d’assistance de liaison.

  La valeur par défaut est False. Si les développeurs souhaitent utiliser la nouvelle fonctionnalité de méthodes de marshaling JNI, ils peuvent définir

  ```xml
  <AndroidGenerateJniMarshalMethods>True</AndroidGenerateJniMarshalMethods>
  ```

  dans leur fichier csproj. Vous pouvez également fournir la propriété sur la ligne de commande via

  ```
  /p:AndroidGenerateJniMarshalMethods=True
  ```

  **Experimental**. Ajouté dans Xamarin.Android 9.2.
  La valeur par défaut est False.

- **AndroidGenerateJniMarshalMethodsAdditionalArguments** &ndash; une propriété de type chaîne qui peut être utilisée pour ajouter des paramètres supplémentaires à l’appel de `jnimarshalmethod-gen.exe`.  Cette propriété est utile pour le débogage et rendre possible l’utilisation d’options telles que `-v`, `-d`, ou `--keeptemp`.

  La valeur par défaut est une chaîne vide. Elle peut être définie dans le fichier .csproj ou sur la ligne de commande. Par exemple :

  ```xml
  <AndroidGenerateJniMarshalMethodsAdditionalArguments>-v -d --keeptemp</AndroidGenerateJniMarshalMethodsAdditionalArguments>
  ```

  ou :

  ```
  /p:AndroidGenerateJniMarshalMethodsAdditionalArguments="-v -d --keeptemp"
  ```

  Ajouté dans Xamarin.Android 9.2.

- **AndroidHttpClientHandlerType** &ndash; contrôle l’implémentation de `System.Net.Http.HttpMessageHandler` par défaut qui sera utilisée par le constructeur `System.Net.Http.HttpClient` par défaut. Sa valeur est un nom de type qualifié d’assembly d’une sous-classe `HttpMessageHandler`, utilisable avec [`System.Type.GetType(string)`](https://docs.microsoft.com/dotnet/api/system.type.gettype?view=netcore-2.0#System_Type_GetType_System_String_).
  Les valeurs les plus couramment utilisées pour cette propriété sont :

  - `Xamarin.Android.Net.AndroidClientHandler`: utilisez les API Java Android pour effectuer des demandes réseau. Cela permet d’accéder aux URL TLS 1.2 lorsque la version Android sous-jacente prend en charge TLS 1.2. Seuls Android 5.0 et les versions ultérieures gèrent TLS 1.2 de façon fiable avec Java.

    Cela correspond à l’option **Android** des pages de propriétés Visual Studio, et à l’option **AndroidClientHandler** dans les pages de propriétés Visual Studio pour Mac.

    L’Assistant Nouveau projet sélectionne cette option pour les nouveaux projets lorsque la **Version minimale d’Android** est configurée sur **Android 5.0 (Lollipop)** ou version ultérieure dans Visual Studio, ou lorsque **Plateformes cibles** est défini sur **Tout dernier** dans Visual Studio pour Mac.

  - Unset/la chaîne vide : cela équivaut à `System.Net.Http.HttpClientHandler, System.Net.Http`

    Cela correspond à l’option **Par défaut** dans les pages de propriétés de Visual Studio.

    L’Assistant Nouveau projet sélectionne cette option pour les nouveaux projets lorsque la **Version minimale d’Android** est configurée sur **Android 4.4.87** ou version antérieure dans Visual Studio, ou lorsque **Plateformes cibles** est défini sur **Développement moderne** ou **Compatibilité maximale** dans Visual Studio pour Mac.

  - `System.Net.Http.HttpClientHandler, System.Net.Http`: utilisez le `HttpMessageHandler`géré.

    Cela correspond à l’option **Managé** dans les pages de propriétés de Visual Studio.

  > [!NOTE]
  > Si la prise en charge de TLS 1.2 est requise sur les versions d’Android antérieures à 5.0, *ou* qu’elle est nécessaire avec `System.Net.WebClient` et les API associées, il faut utiliser `$(AndroidTlsProvider)`.

  > [!NOTE]
  > La prise en charge de cette propriété passe par la définition de la [variable d’environnement `XA_HTTP_CLIENT_HANDLER_TYPE`](~/android/deploy-test/environment.md).
  > Une valeur `$XA_HTTP_CLIENT_HANDLER_TYPE` trouvée dans un fichier avec l’action de génération `@(AndroidEnvironment)` sera prioritaire.

  Ajouté dans Xamarin.Android 6.1.

- **AndroidLinkMode** &ndash; spécifie le type de [liaison](~/android/deploy-test/linker.md) à effectuer sur les assemblys contenus dans le package Android. Utilisé seulement dans les projets d’application Android. La valeur par défaut est *SdkOnly*. Les valeurs autorisées sont :

  - **None** : aucune liaison n’est tentée.

  - **SdkOnly** : la liaison est effectuée seulement sur les bibliothèques de classes de base, pas sur les assemblys de l’utilisateur.

  - **Full** : la liaison est effectuée seulement sur les bibliothèques de classes de base et sur les assemblys de l’utilisateur.

    > [!NOTE]
    > L’utilisation de la valeur `AndroidLinkMode`Full*pour* aboutit généralement à des applications endommagées, en particulier quand la réflexion est utilisée. À éviter, sauf si vous savez *vraiment* ce que vous faites.

  ```xml
  <AndroidLinkMode>SdkOnly</AndroidLinkMode>
  ```

- **AndroidLinkSkip** &ndash; spécifie une liste de noms d’assemblys (`;`) délimités par des points-virgules, sans extensions de fichier, des assemblys qui ne doivent pas être liés. Utilisé seulement dans les projets d’application Android.

  ```xml
  <AndroidLinkSkip>Assembly1;Assembly2</AndroidLinkSkip>
  ```

- **AndroidLinkTool** &ndash; une propriété enum-style avec des valeurs valides de `proguard` ou `r8`. Indique le réducteur de code utilisé pour le code Java. La valeur par défaut est une chaîne vide, ou `proguard` si `$(AndroidEnableProguard)` est `True`. Pour plus d’informations, consultez notre documentation sur [D8 et R8][d8-r8].

  [d8-r8]: https://github.com/xamarin/xamarin-android/blob/master/Documentation/guides/D8andR8.md

- **AndroidLintEnabled** &ndash; une propriété bool qui permet au développeur d’exécuter l’outil de `lint` Android dans le cadre du processus d’empaquetage.

  - **AndroidLintEnabledIssues** &ndash; une liste séparée par des virgules de problèmes de Lint à activer.

  - **AndroidLintDisabledIssues** &ndash; une liste séparée par des virgules de problèmes de Lint à désactiver.

  - **AndroidLintCheckIssues** &ndash; une liste séparée par des virgules de problèmes de Lint à vérifier.
    Remarque : Seuls ces problèmes sont vérifiés.

  - **AndroidLintConfig** &ndash; il s’agit d’une action de génération pour un fichier de configuration de type Lint. Cela peut servir à activer/désactiver les problèmes à vérifier. Plusieurs fichiers dont le contenu est appelé à être fusionné peuvent utiliser cette action de génération.

  Consultez l’[aide de Lint](https://developer.android.com/studio/write/lint) pour plus d’informations sur l’outil `lint` android.

- **AndroidManagedSymbols** &ndash; propriété booléenne qui contrôle si les points de séquence sont générés afin que les informations de nom de fichier et de numéro de ligne puissent être extraites de `Release` traces de la pile.

  Ajouté dans Xamarin.Android 6.1.

- **Fichier androidmanifest** &ndash; spécifie un nom de fichier à utiliser comme modèle pour le [`AndroidManifest.xml`](~/android/platform/android-manifest.md)de l’application.
  Lors de la génération, toutes les autres valeurs nécessaires y sont fusionnées pour produire le fichier `AndroidManifest.xml` effectif.
  `$(AndroidManifest)` doit contenir le nom du package dans l’attribut `/manifest/@package`.

- **AndroidManifestMerger** &ndash; spécifie l’implémentation pour la fusion des fichiers *fichier AndroidManifest. xml* . Il s’agit d’une propriété de style enum dans laquelle `legacy` C# sélectionne l’implémentation d’origine et `manifestmerger.jar` sélectionne l’implémentation Java de Google.

  La valeur par défaut est actuellement `legacy`. Cela passera à `manifestmerger.jar` dans une version ultérieure pour aligner le comportement avec Android Studio.

  La fusion de Google permet la prise en charge de `xmlns:tools="http://schemas.android.com/tools"`, comme décrit dans la [documentation Android][manifest-merger].

  Introduit dans Xamarin. Android 10,2

  [manifest-merger]: https://developer.android.com/studio/build/manifest-merge

- **AndroidMultiDexClassListExtraArgs** &ndash; une propriété de type chaîne qui permet aux développeurs de passer des arguments supplémentaires à l' `com.android.multidex.MainDexListBuilder` lors de la génération du fichier `multidex.keep`.

  Un cas spécifique est si vous obtenez l’erreur suivante durant la compilation `dx`.

  ```
  com.android.dex.DexException: Too many classes in --main-dex-list, main dex capacity exceeded
  ```

  Si vous obtenez cette erreur, vous pouvez ajouter le code suivant au fichier .csproj.

  ```xml
  <DxExtraArguments>--force-jumbo </DxExtraArguments>
  <AndroidMultiDexClassListExtraArgs>--disable-annotation-resolution-workaround</AndroidMultiDexClassListExtraArgs>
  ```

  Ce code devrait permettre au processus `dx` de se dérouler normalement.

  Ajouté dans Xamarin.Android 8.3.

- **AndroidPackageFormat** &ndash; une propriété enum-style avec des valeurs valides de `apk` ou `aab`. Cela indique si vous souhaitez empaqueter l’application Android sous la forme d’un [fichier APK][apk] ou d’un [bundle d’applications Android][bundle]. Les bundles d’applications sont un nouveau format pour les builds `Release` destinées à être envoyées à Google Play. La valeur par défaut est `apk`.

  Lorsque `$(AndroidPackageFormat)` est défini sur `aab`, d’autres propriétés MSBuild sont définies. Celles-ci sont nécessaires pour les bundles d’applications Android :

  - `$(AndroidUseAapt2)` a la valeur `True`.
  - `$(AndroidUseApkSigner)` a la valeur `False`.
  - `$(AndroidCreatePackagePerAbi)` a la valeur `False`.

  [apk]: https://en.wikipedia.org/wiki/Android_application_package
  [bundle]: https://developer.android.com/platform/technology/app-bundle

- **AndroidPackageNamingPolicy** &ndash; une propriété enum pour la spécification des noms de packages Java du code source Java généré.

  Dans Xamarin. Android 10,2 et versions ultérieures, la seule valeur prise en charge est `LowercaseCrc64`.

  Dans Xamarin. Android 10,1, une valeur de `LowercaseMD5` de transition était également disponible et permettait de revenir au style de nom de package Java d’origine tel qu’il était utilisé dans Xamarin. Android 10,0 et versions antérieures. Cette option a été supprimée dans Xamarin. Android 10,2 pour améliorer la compatibilité avec les environnements de génération pour lesquels la conformité FIPS est appliquée.

  Ajouté dans Xamarin. Android 10,1.

- **AndroidR8JarPath** &ndash; le chemin d’accès à `r8.jar` à utiliser avec le compilateur et le shrinkon R8. Correspond par défaut à un chemin dans l’installation de Xamarin.Android. Pour plus d’informations, consultez notre documentation sur [D8 et R8][d8-r8].

- **AndroidSdkBuildToolsVersion** &ndash; le Android SDK package d’outils de génération fournit les outils **AAPT** et **zipalign** , entre autres. Plusieurs versions différentes du package d’outils de génération peuvent être installées en parallèle. Le package d’outils de génération choisi pour l’empaquetage est réalisé en recherchant et en utilisant, le cas échéant, une version « favorite » des outils de génération ; s’il n’en existe *pas*, le package d’outils de génération associé à la version la plus récente sera utilisé.

  La propriété MSBuild `$(AndroidSdkBuildToolsVersion)` contient la version préférée des outils de génération. Le système de génération de Xamarin.Android fournit une valeur par défaut dans `Xamarin.Android.Common.targets`, et vous pouvez remplacer la valeur par défaut dans votre fichier projet et choisir une autre version des outils de génération, si (par exemple) la version la plus récente de aapt plante, alors qu’une version antérieure de aapt est connue pour fonctionner.

- **AndroidSupportedAbis** &ndash; une propriété de type chaîne qui contient une liste délimitée par des points-virgules (`;`) de Abi qui doit être incluse dans le `.apk`.

  Les valeurs prises en charge sont :

  - `armeabi-v7a`
  - `x86`
  - `arm64-v8a` : nécessite Xamarin.Android 5.1 et ultérieur.
  - `x86_64` : nécessite Xamarin.Android 5.1 et ultérieur.

- **AndroidTlsProvider** &ndash; une valeur de chaîne qui spécifie le fournisseur TLS à utiliser dans une application. Les valeurs possibles sont les suivantes :

  - Unset/la chaîne vide : dans Xamarin. Android 7,3 et versions ultérieures, cela équivaut à `btls`.

    dans Xamarin.Android 7.1, équivaut à `legacy`.

    Cela correspond au paramètre **Par défaut** dans les pages de propriétés de Visual Studio.

  - `btls` : utilise [BoringSSL](https://boringssl.googlesource.com/boringssl) pour la communication TLS avec [HttpWebRequest](xref:System.Net.HttpWebRequest).

    Cela permet d’utiliser TLS 1.2 sur toutes les versions d’Android.

    Cela correspond au paramètre **TLS 1.2+ natif** dans les pages de propriétés de Visual Studio.

  - `legacy`: dans Xamarin. Android 10,1 et versions antérieures, utilisez l’implémentation de l’historique SSL géré pour l’interaction réseau. Ceci *ne prend pas* en charge TLS 1.2.

    Cela correspond au paramètre **TLS 1.0 managé** dans les pages de propriétés de Visual Studio.

    Dans Xamarin. Android 10,2 et versions ultérieures, cette valeur est ignorée et le paramètre `btls` est utilisé.

  - `default`: cette valeur est peu susceptible d’être utilisée dans les projets Xamarin. Android. Dans ce cas, la valeur recommandée est une chaîne vide, qui correspond au paramètre **Par défaut** des pages de propriétés Visual Studio.

    La valeur `default` n’est pas disponible dans les pages de propriétés Visual Studio.

    Ceci équivaut à `legacy`.

  Ajouté dans Xamarin.Android 7.1.

- **AndroidUseApkSigner** &ndash; une propriété bool qui permet au développeur d’utiliser le pour l’outil de `apksigner` plutôt que le `jarsigner`.

    Ajouté dans Xamarin.Android 8.2.

- **AndroidUseDefaultAotProfile** &ndash; propriété bool qui permet au développeur de supprimer l’utilisation des profils AOA par défaut.

  Pour supprimer les profils AOA par défaut, affectez à la propriété la valeur `false`.

  Ajouté dans Xamarin. Android 10,1.

- **AndroidUseLegacyVersionCode** &ndash; une propriété booléenne permet au développeur de rétablir le calcul versionCode à son ancien comportement Xamarin. Android 8,2. Elle est RÉSERVÉE aux développeurs possédant des applications dans Google Play Store. Il est fortement recommandé d’utiliser la nouvelle propriété `$(AndroidVersionCodePattern)`.

  Ajouté dans Xamarin.Android 8.2.

- **AndroidUseManagedDesignTimeResourceGenerator** &ndash; une propriété booléenne qui bascule les builds au moment du design pour utiliser l’analyseur de ressources managées plutôt que `aapt`.

  Ajouté dans Xamarin.Android 8.1.

- **AndroidUseSharedRuntime** &ndash; propriété booléenne qui détermine si les packages du *Runtime partagé* sont requis pour exécuter l’application sur le périphérique cible. Le fait de s’appuyer sur les packages de runtime partagé permet de réduire la taille du package d’application, ce qui accélère le processus de création et de déploiement du package, aboutissant à un cycle de génération/déploiement/débogage plus rapide.

  Cette propriété doit être définie sur `True` pour les versions Debug, et sur `False` pour les projets Release.

- **AndroidVersionCodePattern** &ndash; une propriété de type chaîne qui permet au développeur de personnaliser le `versionCode` dans le manifeste.
  Consultez [Création de code de version pour APK](~/android/deploy-test/building-apps/abi-specific-apks.md) pour plus d’informations sur le choix d’un `versionCode`.

  Voici des exemples : si `abi` est `armeabi` et si `versionCode` dans le manifeste est `123`, `{abi}{versionCode}` produit un code de version `1123` quand `$(AndroidCreatePackagePerAbi)` a la valeur True ; sinon il produit la valeur 123.
  Si `abi` est `x86_64` et `versionCode` dans le manifeste est `44`. Ceci produit `544` quand `$(AndroidCreatePackagePerAbi)` est True ; sinon, il produit la valeur `44`.

  Si nous incluons une chaîne avec remplissage à gauche `{abi}{versionCode:0000}`, ceci produit `50044`, car nous remplissons le `versionCode` à gauche avec `0`. Vous pouvez également utiliser le remplissage décimal tel que `{abi}{versionCode:D4}`
  qui fait la même chose que l’exemple précédent.

  Seules les chaînes avec le format de remplissage « 0 » et « Dx » sont prises en charge, car la valeur DOIT être un entier.

  Éléments clés prédéfinis

  - **abi**&ndash; insère le Abi ciblé pour l’application
    - 2 &ndash; `armeabi-v7a`
    - 3 &ndash; `x86`
    - 4 &ndash; `arm64-v8a`
    - 5 &ndash; `x86_64`

  - **minSDK**&ndash; insère la valeur minimale prise en charge du kit de développement logiciel (SDK) à partir de la `AndroidManifest.xml` ou `11` si aucune valeur n’est définie.

  - **versionCode** &ndash; utilise le code de version directement à partir de `Properties\AndroidManifest.xml`.

  Vous pouvez définir des éléments personnalisés en utilisant la propriété `$(AndroidVersionCodeProperties)` (définie ensuite).

  La valeur par défaut est `{abi}{versionCode:D6}`. Si un développeur souhaite conserver l’ancien comportement, vous pouvez la remplacer en définissant la propriété `$(AndroidUseLegacyVersionCode)` sur `true`.

  Ajouté dans Xamarin.Android 7.2.

- **AndroidVersionCodeProperties** &ndash; une propriété de type chaîne qui permet au développeur de définir des éléments personnalisés à utiliser avec le `AndroidVersionCodePattern`. Ils sont sous la forme d’une paire `key=value`. Tous les éléments dans `value` doivent être des valeurs entières. Par exemple : `screen=23;target=$(_AndroidApiLevel)`. Vous constatez que vous pouvez utiliser des propriétés MSBuild existantes ou personnalisées dans la chaîne.

  Ajouté dans Xamarin.Android 7.2.

- **AotAssemblies** &ndash; propriété booléenne qui détermine si les assemblys sont compilés à l’avance en code natif et inclus dans le `.apk`.

  La prise en charge de cette propriété a été ajoutée dans Xamarin.Android 5.1.

  Cette propriété est définie par défaut sur `False`.

- **EmbedAssembliesIntoApk** &ndash; propriété booléenne qui détermine si les assemblys de l’application doivent être incorporés dans le package d’application.

  Cette propriété doit être définie sur `True` pour les versions Release, et sur `False` pour les projets Debug. Sa valeur *doit* être `True` dans les versions Debug si le déploiement rapide ne prend pas en charge l’appareil cible.

  Quand cette propriété est `False`, la propriété MSBuild `$(AndroidFastDeploymentType)` détermine également ce qui doit être incorporé dans le fichier `.apk`, ce qui peut impacter les temps de déploiement et de regénération.

- **EnableLLVM** &ndash; propriété booléenne qui détermine si LLVM doit ou non être utilisé lors de la compilation anticipée des assemblys en code natif.

  Le NDK Android doit être installé pour générer un projet pour lequel cette propriété est activée.

  La prise en charge de cette propriété a été ajoutée dans Xamarin.Android 5.1.

  Cette propriété est définie par défaut sur `False`.

  Cette propriété est ignorée sauf si la propriété MSBuild `$(AotAssemblies)` est définie sur `True`.

- **EnableProguard** &ndash; propriété booléenne qui détermine si [ProGuard](https://developer.android.com/tools/help/proguard.html) est exécuté dans le cadre du processus d’empaquetage pour lier du code Java.

  La prise en charge de cette propriété a été ajoutée dans Xamarin.Android 5.1.

  Cette propriété est définie par défaut sur `False`.

  Quand elle a la valeur `True`, les fichiers [ProguardConfiguration](#ProguardConfiguration) sont utilisés pour contrôler l’exécution de `proguard`.

- **JavaMaximumHeapSize** &ndash; spécifie la valeur du paramètre **java**
  `-Xmx` à utiliser lors de la génération du fichier `.dex` dans le cadre du processus d’empaquetage. Si non spécifié, l’option `-Xmx` fournit **java** avec la valeur `1G`. Ceci est couramment requis sur Windows par rapport à d’autres plateformes.

  La spécification de cette propriété est nécessaire si la cible [`_CompileDex` lève une erreur `java.lang.OutOfMemoryError`](https://bugzilla.xamarin.com/show_bug.cgi?id=18327).

  Personnalisez la valeur en changeant :

  ```xml
  <JavaMaximumHeapSize>1G</JavaMaximumHeapSize>
  ```

- **JavaOptions** &ndash; spécifie des options de ligne de commande supplémentaires à passer à **java** lors de la génération du fichier `.dex`.

- **LinkerDumpDependencies** &ndash; une propriété bool qui active la génération du fichier de dépendances de l’éditeur de liens. Ce fichier peut être utilisé en tant qu’entrée pour l’outil [illinkanalyzer](https://github.com/mono/linker/blob/master/src/analyzer/README.md).

  La valeur par défaut est False.

- **MandroidI18n** &ndash; spécifie la prise en charge de l’internationalisation incluse avec l’application, comme les tables de classement et de tri. La valeur est une liste (délimitée par des virgules ou des points-virgules) d’une ou plusieurs des valeurs suivantes, qui ne respectent pas la casse :

  - **None** : n’inclure aucun encodage supplémentaire.

  - **All** : inclure tous les encodages disponibles.

  - **CJC**: inclure les encodages chinois, japonais et coréen tels que le *japonais (EUC)* \[enc-JP, CP51932\] *, japonais (Shift-JIS)* \[ISO-2022-JP, Maj\_JIS, cp932\] *, Japanese (JIS)* \[CP50220\], *chinois simplifié (GB2312)* \[GB2312, CP936\], *coréen (UHC)* \[KS\_c\_5601-1987, CP949\], *coréen (EUC)* \[EUC-KR, CP51949\], *chinois traditionnel (Big5)* \[Big5, cp950\]et *chinois simplifié (GB18030)* \[GB18030, CP54936\].

  - **Mideast**: inclure des encodages intermédiaires tels que le *turc (Windows)* \[ISO-8859-9, CP1254\], *hébreu (windows)* \[Windows-1255, CP1255\], *arabe (Windows)* \[Windows-1256, CP1256\], *Arabic (ISO)* \[ISO-8859-6, CP28596\], *hébreu (ISO)* \[iso-8859-8, CP28598\], *latin 5 (ISO)* \[ISO-8859-9, CP28599\]et *hébreu (alternative ISO )* \[ISO-8859-8, CP38598\].

  - **Autre**: inclure d’autres encodages comme *cyrillique (Windows)* \[cp1251 correspond à\], *Baltique (windows)* \[ISO-8859-4, cp1257\], *vietnamien (Windows)* \[CP1258\], *cyrillique (koi8-r)* \[koi8-r, cp1251 correspond à\], *ukrainien (KOI8-u)* \[KOI8-u, cp1251 correspond À\], *balte (ISO)* \[iso-8859-4, cp1257\], *cyrillique (ISO)* \[ISO-8859-5, cp1251 correspond à\], *ISCII Davenagari* \[x-ISCII-de, CP57002\], *ISCII bengali* \[x-ISCII-is, CP57003\], *ISCII tamoul* \[x-ISCII-ta, CP57004\], *ISCII télougou* \[x-ISCII-te, CP57005\], *ISCII assamais* \[x-ISCII-As, CP57006\], *ISCII Odia* \[x-ISCII-or, CP57007\], *ISCII kannada* \[x-ISCII-ka, CP57008\], *ISCII Malayalam* \[x-ISCII-ma, CP57009\], *iscii Goudjrati* \[x-ISCII-gu, CP57010\], *ISCII pendjabi* \[x-iscii-PA, CP57011\]et *thaï (Windows)* \[CP874\].

  - **Rare**: inclure des encodages rares tels qu' *IBM EBCDIC (turc)* \[CP1026\] *, IBM EBCDIC (systèmes ouverts Latin 1)* \[CP1047\] *, IBM EBCDIC (US-Canada avec Euro)* \[CP1140\], *ibm EBCDIC (Allemagne avec Euro)* \[CP1141\], *ibm EBCDIC (Danemark/Norvège avec Euro)* \[CP1142\], *IBM EBCDIC (Finlande/Suède avec Euro)* \[CP1143\], *IBM EBCDIC (Italie avec euro )* \[CP1144\], *IBM ebcdic (Amérique latine/Espagne avec euro)* \[CP1145\], *IBM EBCDIC (Royaume-uni avec euro)* \[CP1146\], *IBM EBCDIC (France avec* euro) \[CP1147\], IBM *EBCDIC (international avec Euro)* \[CP1148\], ibm EBCDIC ( *islandais avec Euro)* \[CP1149\], *IBM EBCDIC (Allemagne)* \[CP20273\], *IBM EBCDIC ( Danemark/Norvège)* \[CP20277\], *IBM EBCDIC (Finlande/suède)* \[CP20278\], *IBM ebcdic (italie)* \[CP20280\], *IBM ebcdic (Amérique latine/Espagne)* \[CP20284\], ibm EBCDIC ( *Royaume-Uni)* \[CP20285\], *IBM EBCDIC (japonais katakana Extended)* \[CP20290\], *IBM EBCDIC (France)* \[CP20297\], *IBM EBCDIC (arabe )* \[CP20420\], *IBM EBCDIC (hébreu)* \[CP20424\], *IBM ebcdic (islandais)* \[CP20871\], *IBM EBCDIC (cyrillique-serbe, bulgare)* \[CP21025\], *IBM ebcdic (US-Canada)* \[CP37\], *IBM EBCDIC (international)* \[CP500\], *arabe (ASMO 708)* \[CP708\], *Centre européen (dos)* \[CP852\] *, Cyrillique (dos)* \[CP855\], *turc (dos)* \[CP857\], Europe *occidentale (dos avec Euro)* \[CP858\], *hébreu (dos)* \[CP862\], *arabe (dos)* \[CP864\], *russe (dos)* \[cp866\], *grec (dos)* \[CP869\], *IBM EBCDIC (latin 2)* \[CP870\]et *IBM EBCDIC (grec)* \[\]CP875.

  - **Ouest**: inclure les encodages occidentaux tels que l’Europe *occidentale (Mac)* \[Macintosh, CP10000\], *islandais (Mac)* \[x-Mac-islandais, CP10079\], *Centre européen (Windows)* \[ISO-8859-2, CP1250\], europe *occidentale (Windows)* \[ISO-8859-1, CP1252\], *grec (Windows)* \[iso-8859-7, CP1253\], *Centre européen (ISO)* \[ISO-8859-2, CP28592\], *Latin 3 (ISO)* \[iso-8859-3, CP28593\], *grec (ISO)* \[ISO-8859-7, CP28597\], *Latin 9 (iso)* \[ISO-8859-15, CP28605\], *OEM États-Unis* \[CP437\], Europe *occidentale (dos)* \[cp850\], *Portugais (dos)* \[CP860\], *islandais (dos)* \[CP861\], *français canadien (dos)* \[CP863\]et *nordique (dos)* \[CP865\].

  ```xml
  <MandroidI18n>West</MandroidI18n>
  ```

- **MonoSymbolArchive** &ndash; propriété booléenne qui contrôle si `.mSYM` artefacts sont créés pour une utilisation ultérieure avec `mono-symbolicate`, pour extraire &ldquo;nom de fichier&rdquo; réel et les informations de numéro de ligne des traces de la pile de mise en sortie.

  Elle a la valeur True par défaut pour les applications &ldquo;Release&rdquo; pour lesquelles les symboles de débogage sont activés : `$(EmbedAssembliesIntoApk)` a la valeur True, `$(DebugSymbols)` a la valeur True et `$(Optimize)` a la valeur True.

  Ajouté dans Xamarin.Android 7.1.

### <a name="binding-project-build-properties"></a>Liaison des propriétés de build du projet

Les propriétés MSBuild suivantes sont utilisées avec les [projets de liaison](~/android/platform/binding-java-library/index.md) :

- **AndroidClassParser** &ndash; une propriété de type chaîne qui contrôle la manière dont `.jar` fichiers sont analysés. Les valeurs possibles incluent :

  - **class-parse** : utilise `class-parse.exe` pour analyser le bytecode Java directement, sans l’aide d’une machine virtuelle Java. Cette valeur est expérimentale.

  - **jar2xml** : utilisez `jar2xml.jar` pour utiliser la réflexion Java pour extraire des types et des membres de réflexion d’un fichier `.jar`.

  Les avantages de `class-parse` par rapport à `jar2xml` sont :

  - `class-parse` peut extraire des noms de paramètre du bytecode Java qui contient des symboles de *débogage* (par exemple du bytecode compilé avec `javac -g`).

  - `class-parse` « n’ignore pas » les classes qui héritent de ou qui contiennent des membres de types qui ne peuvent pas être résolus.

  **Experimental**. Ajouté dans Xamarin.Android 6.0.

  La valeur par défaut est `jar2xml`.

  La valeur par défaut changera dans une version ultérieure.

- **AndroidCodegenTarget** &ndash; une propriété de type chaîne qui contrôle l’Abi cible de génération de code. Les valeurs possibles incluent :

  - **XamarinAndroid**: utilise l’API de liaison JNI présente dans la mesure où mono pour Android 1,0. La liaison des assemblys générés avec Xamarin.Android 5.0 ou ultérieur peut s’exécuter seulement sur Xamarin.Android 5.0 ou ultérieur (ajouts d’API/ABI), mais la *source* est compatible avec les versions antérieures du produit.

  - **XAJavaInterop1** : utilise Java.Interop pour les appels JNI. La liaison des assemblys avec `XAJavaInterop1` peut être générée et s’exécuter seulement avec Xamarin.Android 6.1 ou ultérieur. Xamarin.Android 6.1 et ultérieur effectue la liaison de `Mono.Android.dll` avec cette valeur.

    Les avantages de `XAJavaInterop1` sont :

    - Assemblys plus petits.

    - Mise en cache de `jmethodID` pour les appels de méthode `base`, dès lors que tous les autres types de liaison de la hiérarchie d’héritage sont générés avec `XAJavaInterop1` ou ultérieur.

    - Mise en cache de `jmethodID` pour les constructeurs de wrapper appelables par Java pour les sous-classes managées.

    La valeur par défaut est `XAJavaInterop1`.

### <a name="resource-properties"></a>Propriétés de ressource

Les propriétés des ressources contrôlent la génération du fichier `Resource.designer.cs`, qui permet d’accéder aux ressources Android.

- **AndroidAapt2CompileExtraArgs** &ndash; spécifie des options de ligne de commande supplémentaires à passer à la commande **aapt2 compile** lors du traitement des ressources et ressources Android.

  Ajouté dans Xamarin.Android 9.1.

- **AndroidAapt2LinkExtraArgs** &ndash; spécifie des options de ligne de commande supplémentaires à passer à la commande **aapt2 Link** lors du traitement des ressources et ressources Android.

  Ajouté dans Xamarin.Android 9.1.

- **AndroidExplicitCrunch** &ndash; si vous générez une application avec un très grand nombre de drawables locaux, une build initiale (ou une reconstruction) peut prendre plusieurs minutes. Pour accélérer le processus de génération, essayez en incluant cette propriété et en la définissant sur `True`. Quand cette propriété est définie, le processus de génération compresse les fichiers .png.

  Remarque : Cette option n’est pas compatible avec l’option `$(AndroidUseAapt2)`. Si `$(AndroidUseAapt2)` est activé, cette fonctionnalité est désactivée. Si vous souhaitez continuer à utiliser cette fonctionnalité, définissez `$(AndroidUseAapt2)` sur `False`.

  **Experimental**. Ajouté dans Xamarin.Android 7.0.

- **AndroidResgenExtraArgs** &ndash; spécifie des options de ligne de commande supplémentaires à passer à la commande **AAPT** lors du traitement des ressources et ressources Android.

- **AndroidResgenFile** &ndash; spécifie le nom du fichier de ressources à générer. Le modèle par défaut définit cette valeur sur `Resource.designer.cs`.

- **AndroidUseAapt2** &ndash; une propriété bool qui permet au développeur de contrôler l’utilisation de l’outil `aapt2` pour l’empaquetage.
  Par défaut, la valeur est false et nous utilisons `aapt`.
  Si le développeur souhaite utiliser la nouvelle fonctionnalité `aapt2`, il peut définir

  ```xml
  <AndroidUseAapt2>True</AndroidUseAapt2>
  ```

  dans leur fichier csproj. Vous pouvez également fournir la propriété sur la ligne de commande via

  ```
  /p:AndroidUseAapt2=True
  ```

  Ajouté dans Xamarin.Android 8.3.

- **MonoAndroidResourcePrefix** &ndash; spécifie un *préfixe de chemin d’accès* qui est supprimé du début des noms de fichiers avec une action de génération de `AndroidResource`. Ceci permet de changer l’emplacement où se trouvent les ressources.

  La valeur par défaut est `Resources`. Changez ceci en `res` pour la structure de projet Java.

<a name="Signing_Properties" />

### <a name="signing-properties"></a>Propriétés de signature

Les propriétés de signature contrôlent comment le package d’application est signé pour qu’il puisse être installé sur un appareil Android. Pour permettre une itération plus rapide des générations, les tâches Xamarin.Android ne signent pas les packages pendant le processus de génération, car l’opération de signature est assez lente. Au lieu de cela, les packages sont signés (si nécessaire) avant l’installation ou pendant l’exportation, par l’IDE ou par la cible de génération *Install*. Le fait d’appeler la cible *SignAndroidPackage* produit un package avec le suffixe `-Signed.apk` dans le répertoire de sortie.

Par défaut, la cible de signature génère si nécessaire une nouvelle clé de signature de débogage. Si vous voulez utiliser une clé spécifique, par exemple sur un serveur de builds, vous pouvez utiliser les propriétés MSBuild suivantes :

- **AndroidDebugKeyAlgorithm** &ndash; spécifie l’algorithme par défaut à utiliser pour le `debug.keystore`. Ce champ est défini sur `RSA` par défaut.

- **AndroidDebugKeyValidity** &ndash; spécifie la validité par défaut à utiliser pour le `debug.keystore`. Ce champ est défini sur `10950`, `30 * 365` ou `30 years` par défaut.

- **AndroidDebugStoreType** &ndash; spécifie le format de fichier du magasin de clés à utiliser pour le `debug.keystore`. Ce champ est défini sur `pkcs12` par défaut.

  Ajouté dans Xamarin. Android 10,2.

- **AndroidKeyStore** &ndash; une valeur booléenne qui indique si les informations de signature personnalisées doivent être utilisées. La valeur par défaut est `False`, qui signifie que la clé de signature de débogage par défaut est utilisée pour signer les packages.

- **AndroidSigningKeyAlias** &ndash; spécifie l’alias de la clé dans le magasin de clés. Il s’agit de la valeur **keytool -alias** utilisée lors de la création du magasin de clés.

- **AndroidSigningKeyPass** &ndash; spécifie le mot de passe de la clé dans le fichier de magasin de clés. C’est la valeur entrée quand `keytool` demande **Entrez le mot clé pour $(AndroidSigningKeyAlias)** .

  Dans Xamarin. Android 10,0 et versions antérieures, cette propriété ne prend en charge que les mots de passe en texte brut.

  Dans Xamarin. Android 10,1 et versions ultérieures, cette propriété prend également en charge les préfixes `env:` et `file:` qui peuvent être utilisés pour spécifier une variable d’environnement ou un fichier qui contient le mot de passe. Ces options permettent d’empêcher l’affichage du mot de passe dans les journaux de génération.

  Par exemple, pour utiliser une variable d’environnement nommée *AndroidSigningPassword*:

  ```xml
  <PropertyGroup>
      <AndroidSigningKeyPass>env:AndroidSigningPassword</AndroidSigningKeyPass>
  </PropertyGroup>
  ```

  Pour utiliser un fichier situé dans `C:\Users\user1\AndroidSigningPassword.txt`:

  ```xml
  <PropertyGroup>
      <AndroidSigningKeyPass>file:C:\Users\user1\AndroidSigningPassword.txt</AndroidSigningKeyPass>
  </PropertyGroup>
  ```

  > [!NOTE]
  > Le préfixe `env:` n’est pas pris en charge lorsque `$(AndroidPackageFormat)` est défini sur `aab`.

- **AndroidSigningKeyStore** &ndash; spécifie le nom de fichier du fichier de magasin de clés créé par `keytool`. Ceci correspond à la valeur fournie pour l’option **keytool -keystore**.

- **AndroidSigningStorePass** &ndash; spécifie le mot de passe à `$(AndroidSigningKeyStore)`. C’est la valeur fournie à `keytool` lors de la création du fichier de magasin de clés et demandée avec **Entrez le mot de passe du magasin de clés :** .

  Dans Xamarin. Android 10,0 et versions antérieures, cette propriété ne prend en charge que les mots de passe en texte brut.

  Dans Xamarin. Android 10,1 et versions ultérieures, cette propriété prend également en charge les préfixes `env:` et `file:` qui peuvent être utilisés pour spécifier une variable d’environnement ou un fichier qui contient le mot de passe. Ces options permettent d’empêcher l’affichage du mot de passe dans les journaux de génération.

  Par exemple, pour utiliser une variable d’environnement nommée *AndroidSigningPassword*:

  ```xml
  <PropertyGroup>
      <AndroidSigningStorePass>env:AndroidSigningPassword</AndroidSigningStorePass>
  </PropertyGroup>
  ```

  Pour utiliser un fichier situé dans `C:\Users\user1\AndroidSigningPassword.txt`:

  ```xml
  <PropertyGroup>
      <AndroidSigningStorePass>file:C:\Users\user1\AndroidSigningPassword.txt</AndroidSigningStorePass>
  </PropertyGroup>
  ```

  > [!NOTE]
  > Le préfixe `env:` n’est pas pris en charge lorsque `$(AndroidPackageFormat)` est défini sur `aab`.

- **JarsignerTimestampAuthorityCertificateAlias** &ndash; cette propriété vous permet de spécifier un alias dans le magasin de clés pour une autorité d’horodatage.
  Pour plus d’informations, consultez la documentation relative à la [prise en charge des horodateurs de signature](https://docs.oracle.com/javase/8/docs/technotes/guides/security/time-of-signing.html) Java.

  ```xml
  <PropertyGroup>
      <JarsignerTimestampAuthorityCertificateAlias>Alias</JarsignerTimestampAuthorityCertificateAlias>
  </PropertyGroup>
  ```

- **JarsignerTimestampAuthorityUrl** &ndash; cette propriété vous permet de spécifier une URL vers un service d’autorité d’horodatage. Vous pouvez l’utiliser pour vous assurer que votre signature de `.apk` comprend un horodateur.
  Pour plus d’informations, consultez la documentation relative à la [prise en charge des horodateurs de signature](https://docs.oracle.com/javase/8/docs/technotes/guides/security/time-of-signing.html) Java.

  ```xml
  <PropertyGroup>
      <JarsignerTimestampAuthorityUrl>http://example.tsa.url</JarsignerTimestampAuthorityUrl>
  </PropertyGroup>
  ```

Par exemple, considérons l’appel de `keytool` suivant :

```shell
$ keytool -genkey -v -keystore filename.keystore -alias keystore.alias -keyalg RSA -keysize 2048 -validity 10000
Enter keystore password: keystore.filename password
Re-enter new password: keystore.filename password
...
Is CN=... correct?
  [no]:  yes

Generating 2,048 bit RSA key pair and self-signed certificate (SHA1withRSA) with a validity of 10,000 days
        for: ...
Enter key password for keystore.alias
        (RETURN if same as keystore password): keystore.alias password
[Storing filename.keystore]
```

Pour utiliser le magasin de clés généré ci-dessus, utilisez le groupe de propriétés :

```xml
<PropertyGroup>
    <AndroidKeyStore>True</AndroidKeyStore>
    <AndroidSigningKeyStore>filename.keystore</AndroidSigningKeyStore>
    <AndroidSigningStorePass>keystore.filename password</AndroidSigningStorePass>
    <AndroidSigningKeyAlias>keystore.alias</AndroidSigningKeyAlias>
    <AndroidSigningKeyPass>keystore.alias password</AndroidSigningKeyPass>
</PropertyGroup>
```

<a name="Build_Actions" />

## <a name="build-actions"></a>Actions de génération

Les *actions de génération* sont [appliquées aux fichiers](https://docs.microsoft.com/visualstudio/msbuild/common-msbuild-project-items) du projet et contrôlent la façon dont le fichier est traité.

### <a name="androidaarlibrary"></a>AndroidAarLibrary

Il est recommandé d’utiliser l’action de génération de `AndroidAarLibrary` pour référencer directement les fichiers .aar. Elle est le plus souvent utilisée par les composants Xamarin, notamment pour inclure des références à des fichiers .aar requis pour faire fonctionner Google Play et d’autres services.

Les fichiers comportant cette action de génération seront traités de la même manière que les ressources des projets de la bibliothèque. Le .aar sera extrait dans le répertoire intermédiaire. Ensuite, les composants, les ressources et les fichiers .jar seront inclus dans les groupes d’articles correspondants.

### <a name="androidboundlayout"></a>AndroidBoundLayout

Indique que du code-behind doit être généré pour le fichier de disposition si la propriété `AndroidGenerateLayoutBindings` est définie sur `false`. Concernant tous les autres aspects, cette action est identique à l’action `AndroidResource` décrite ci-dessus. Cette action peut être utilisée **uniquement** avec les fichiers de disposition :

```xml
<AndroidBoundLayout Include="Resources\layout\Main.axml" />
```

<a name="AndroidEnvironment" />

### <a name="androidenvironment"></a>AndroidEnvironment

Les fichiers avec une action de génération `AndroidEnvironment` sont utilisés pour [initialiser des variables d’environnement et des propriétés système lors du démarrage du processus](~/android/deploy-test/environment.md).
L’action de génération `AndroidEnvironment` peut être appliquée à plusieurs fichiers : dans ce cas, ils sont évalués sans suivre un ordre particulier (ne spécifiez donc pas la même variable d’environnement ou la même propriété système dans plusieurs fichiers).

### <a name="androidfragmenttype"></a>AndroidFragmentType

Spécifie le type complet par défaut à utiliser pour tous les éléments de disposition `<fragment>` pendant la génération du code des liaisons de disposition. Par défaut, la propriété est définie sur le type `Android.App.Fragment` Android standard.

### <a name="androidjavalibrary"></a>AndroidJavaLibrary

Les fichiers avec une action de génération `AndroidJavaLibrary` sont des archives Java (des fichiers `.jar`) qui sont inclus dans le package Android final.

### <a name="androidjavasource"></a>AndroidJavaSource

Les fichiers avec une action de génération `AndroidJavaSource` contiennent du code source Java qui est inclus dans le package Android final.

### <a name="androidlintconfig"></a>AndroidLintConfig

L’action de génération « AndroidLintConfig » doit être utilisée conjointement avec la propriété de build `AndroidLintEnabled`. Les fichiers avec cette action de génération sont fusionnés et passés à l’outil `lint` android. Ces fichiers doivent être des fichiers XML qui contiennent des informations sur les tests à activer et désactiver.

Consultez la [documentation lint](https://developer.android.com/studio/write/lint) pour plus d’informations.

### <a name="androidnativelibrary"></a>AndroidNativeLibrary

Les [bibliothèques natives](~/android/platform/native-libraries.md) sont ajoutées à la build en définissant leur action de génération sur `AndroidNativeLibrary`.

Notez que comme Android prend en charge plusieurs ABI (Application Binary Interface), le système de génération doit savoir pour quelle ABI la bibliothèque native est générée. Vous pouvez faire cela de deux façons :

1. « Détection » du chemin.
2. Avec l’attribut d’élément `Abi`.

Avec la détection de chemin, le nom du répertoire parent de la bibliothèque native est utilisé pour spécifier l’ABI ciblée par la bibliothèque. Ainsi, si vous ajoutez `lib/armeabi-v7a/libfoo.so` à la build, l’ABI sera « détectée » en tant que `armeabi-v7a`.

#### <a name="item-attribute-name"></a>Nom d’attribut d’élément

**Abi** &ndash; spécifie l’Abi de la bibliothèque native.

```xml
<ItemGroup>
  <AndroidNativeLibrary Include="path/to/libfoo.so">
    <Abi>armeabi-v7a</Abi>
  </AndroidNativeLibrary>
</ItemGroup>
```

### <a name="androidresource"></a>AndroidResource

Tous les fichiers avec une action de génération *AndroidResource* sont compilés en ressources Android pendant le processus de génération et rendus accessible via `$(AndroidResgenFile)`.

```xml
<ItemGroup>
  <AndroidResource Include="Resources\values\strings.xml" />
</ItemGroup>
```

Les utilisateurs plus expérimentés souhaitent éventuellement avoir des ressources différentes utilisées dans des configurations différentes, mais avec le même chemin effectif. Il faut pour cela avoir plusieurs répertoires de ressources et des fichiers avec les mêmes chemins relatifs au sein de ces différents répertoires, et utiliser des conditions MSBuild pour inclure de façon conditionnelle des fichiers différents dans les différentes configurations. Par exemple :

```xml
<ItemGroup Condition="'$(Configuration)'!='Debug'">
  <AndroidResource Include="Resources\values\strings.xml" />
</ItemGroup>
<ItemGroup  Condition="'$(Configuration)'=='Debug'">
  <AndroidResource Include="Resources-Debug\values\strings.xml"/>
</ItemGroup>
<PropertyGroup>
  <MonoAndroidResourcePrefix>Resources;Resources-Debug<MonoAndroidResourcePrefix>
</PropertyGroup>
```

**LogicalName** &ndash; spécifie le chemin d’accès de la ressource explicitement. Permet de &ldquo;définir des alias&rdquo; pour les fichiers, de sorte qu’ils soient disponibles sous plusieurs noms de ressource distincts.

```xml
<ItemGroup Condition="'$(Configuration)'!='Debug'">
  <AndroidResource Include="Resources/values/strings.xml"/>
</ItemGroup>
<ItemGroup Condition="'$(Configuration)'=='Debug'">
  <AndroidResource Include="Resources-Debug/values/strings.xml">
    <LogicalName>values/strings.xml</LogicalName>
  </AndroidResource>
</ItemGroup>
```

### <a name="androidresourceanalysisconfig"></a>AndroidResourceAnalysisConfig

L’action de génération `AndroidResourceAnalysisConfig` marque un fichier comme un fichier de configuration de niveau de gravité pour l’outil de diagnostic Xamarin Android Designer Layout. Actuellement utilisé uniquement dans l’éditeur de disposition et non pour les messages de génération.

Pour plus d’informations, consultez la documentation sur l' [analyse des ressources Android](https://aka.ms/androidresourceanalysis) .

Ajouté dans Xamarin. Android 10,2.

### <a name="content"></a>Contenu

L’action de génération `Content` normale n’est pas prise en charge (comme nous n’avons pas trouvé comment la prendre en charge sans une étape de première exécution éventuellement coûteuse en ressources).

À compter de Xamarin.Android 5.1, une tentative d’utilisation de l’action de génération `@(Content)` produit un avertissement `XA0101`.

### <a name="linkdescription"></a>LinkDescription

Des fichiers avec une action de génération *LinkDescription* sont utilisés pour [contrôler le comportement de l’éditeur de liens](~/cross-platform/deploy-test/linker.md).

<a name="ProguardConfiguration" />

### <a name="proguardconfiguration"></a>ProguardConfiguration

Les fichiers avec une action de génération *ProguardConfiguration* contiennent des options qui permettent de contrôler le comportement de `proguard`. Pour plus d’informations sur cette action de génération, consultez [ProGuard](~/android/deploy-test/release-prep/proguard.md).

Ces fichiers sont ignorés sauf si la propriété MSBuild `$(EnableProguard)` est définie sur `True`.

## <a name="target-definitions"></a>Définitions de cibles

Les parties spécifiques à Xamarin.Android du processus de génération sont définies dans `$(MSBuildExtensionsPath)\Xamarin\Android\Xamarin.Android.CSharp.targets`, mais des cibles normales spécifiques au langage comme *Microsoft.CSharp.targets* sont également nécessaires pour générer l’assembly.

Les propriétés de génération suivantes doivent être définies avant l’importation des cibles de langage :

```xml
<PropertyGroup>
  <TargetFrameworkIdentifier>MonoDroid</TargetFrameworkIdentifier>
  <MonoDroidVersion>v1.0</MonoDroidVersion>
  <TargetFrameworkVersion>v2.2</TargetFrameworkVersion>
</PropertyGroup>
```

Toutes ces cibles et propriétés peuvent être incluses pour C# en important *Xamarin.Android.CSharp.targets* :

```xml
<Import Project="$(MSBuildExtensionsPath)\Xamarin\Android\Xamarin.Android.CSharp.targets" />
```

Ce fichier peut être facilement adapté pour d’autres langages.
