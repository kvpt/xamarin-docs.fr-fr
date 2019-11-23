---
title: Processus de génération
ms.prod: xamarin
ms.assetid: 3BE5EE1E-3FF6-4E95-7C9F-7B443EE3E94C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/22/2019
ms.openlocfilehash: 06e40fce69ee6d614bcf27bd563d9452595bd6ab
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028153"
---
# <a name="build-process"></a>Processus de génération

## <a name="overview"></a>Overview

Le processus de génération de Xamarin.Android est chargé de tout rassembler : [génération de `Resource.designer.cs` ](~/android/internals/api-design.md), prise en charge de `AndroidAsset`, `AndroidResource` et d’autres [actions de génération](#Build_Actions), génération de [wrappers appelables par Android](~/android/platform/java-integration/android-callable-wrappers.md) et génération d’un fichier `.apk` pour l’exécution sur les appareils Android.

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

- **Clean** &ndash; supprime tous les fichiers générés par le processus de génération.

- **Install** &ndash; installe le package sur l’appareil par défaut ou sur l’appareil virtuel.

- **Uninstall** &ndash; désinstalle le package de l’appareil par défaut ou de l’appareil virtuel.

- **SignAndroidPackage** &ndash; crée et signe le package (`.apk`). À utiliser avec `/p:Configuration=Release` pour générer des packages « Release » autonomes.

- **UpdateAndroidResources** &ndash; met à jour le fichier `Resource.designer.cs`. Cette cible est généralement appelée par l’IDE quand de nouvelles ressources sont ajoutées au projet.

## <a name="build-extension-points"></a>Créer des points d’extension

Le système de génération Xamarin.Android expose quelques points d’extension publics pour les utilisateurs souhaitant utiliser notre processus de génération. Pour utiliser l’un de ces points d’extension, vous devez ajouter votre cible personnalisée à la propriété MSBuild appropriée dans un `PropertyGroup`. Exemple :

```xml
<PropertyGroup>
   <AfterGenerateAndroidManifest>
      $(AfterGenerateAndroidManifest);
      YourTarget;
   </AfterGenerateAndroidManifest>
</PropertyGroup>
```

Un mot de prudence pour étendre le processus de génération : s’il n’est pas écrit correctement, les extensions de build peuvent affecter les performances de votre Build, en particulier si elles s’exécutent à chaque Build. Il est vivement recommandé de lire la [documentation](https://docs.microsoft.com/visualstudio/msbuild/msbuild) MSBuild avant d’implémenter ces extensions.

- **AfterGenerateAndroidManifest** &ndash; Les cibles listées dans cette propriété seront exécutées juste après la cible interne `_GenerateJavaStubs`. C’est là que le fichier `AndroidManifest.xml` est généré dans `$(IntermediateOutputPath)`. Par conséquent, si vous souhaitez apporter des modifications au fichier `AndroidManifest.xml` généré, vous pouvez le faire à l’aide de ce point d’extension.

  Ajouté dans Xamarin.Android 9.4.

- **BeforeGenerateAndroidManifest** &ndash; Les cibles listées dans cette propriété seront exécutées juste avant `_GenerateJavaStubs`.

  Ajouté dans Xamarin.Android 9.4.

## <a name="build-properties"></a>Propriétés de build

Les propriétés MSBuild contrôlent le comportement des cibles. Elles sont spécifiées dans le fichier projet, par exemple **MyApp.csproj**, dans un [élément MSBuild PropertyGroup](https://docs.microsoft.com/visualstudio/msbuild/propertygroup-element-msbuild).

- **Configuration** &ndash; spécifie la configuration de build à utiliser, comme « Debug » ou « Release ». La propriété Configuration est utilisée pour déterminer les valeurs par défaut pour d’autres propriétés qui déterminent le comportement de la cible. Vous pouvez créer des configurations supplémentaires dans votre IDE.

  *Par défaut*, la configuration `Debug` fait que les cibles `Install` et `SignAndroidPackage` créent un package Android plus petit qui nécessite la présence d’autres fichiers et packages pour fonctionner.

  Dans la configuration `Release` par défaut, les cibles `Install` et `SignAndroidPackage` créent un package Android *autonome* et utilisable sans installer d’autres packages ou fichiers.

- **DebugSymbols** &ndash; une valeur booléenne qui détermine si le package Android est *débogable*, en combinaison avec la propriété `$(DebugType)`. Un package débogable contient des symboles de débogage, définit l’attribut `//application/@android:debuggable` sur `true` et ajoute automatiquement l’autorisation `INTERNET` de façon à ce qu’un débogueur puisse s’attacher au processus. Une application est débogable si `DebugSymbols` est `True` *, et si* `DebugType` est une chaîne vide ou `Full`.

- **DebugType** &ndash; spécifie le [type de symboles de débogage](https://docs.microsoft.com/visualstudio/msbuild/csc-task) à générer dans le cadre de la build, ce qui détermine si l’application est débogable. Les valeurs possibles incluent notamment :

  - **Full** : des symboles complets sont générés. Si la propriété MSBuild `DebugSymbols` est également définie sur `True`, le package d’application est débogable.

  - **PdbOnly**: les symboles « PDB » sont générés. Le package d’application ne sera *pas* débogable.

  Si `DebugType` n’est pas défini ou est une chaîne vide, la propriété `DebugSymbols` contrôle si l’application est ou non débogable.

  - **AndroidGenerateLayoutBindings** &ndash; Active la génération de [code-behind de disposition](https://github.com/xamarin/xamarin-android/blob/master/Documentation/guides/LayoutCodeBehind.md) si la valeur est `true` ou la désactive complètement si la valeur est `false`. La valeur par défaut est `false`.

### <a name="install-properties"></a>Propriétés d’installation

Les propriétés d’installation contrôlent le comportement des cibles `Install` et `Uninstall`.

- **AdbTarget** &ndash; spécifie l’appareil cible Android où le package Android peut être installé ou supprimé. La valeur de cette propriété est la même que celle de l’option de l’appareil cible[ `adb`](https://developer.android.com/tools/help/adb.html#issuingcommands) :

  ```bash
  # Install package onto emulator via -e
  # Use `/Library/Frameworks/Mono.framework/Commands/msbuild` on OS X
  MSBuild /t:Install ProjectName.csproj /p:AdbTarget=-e
  ```

### <a name="packaging-properties"></a>Propriétés de packaging

Les propriétés de packaging contrôlent la création du package Android et sont utilisées par les cibles `Install` et `SignAndroidPackage`.
Les [propriétés de signature](#Signing_Properties) sont également impliquées pour le packaging des applications d’une version.

- **AndroidApkDigestAlgorithm** &ndash; Valeur de chaîne qui spécifie l’algorithme Digest à utiliser avec `jarsigner -digestalg`.

  La valeur par défaut est `SHA1` pour les fichiers APK, et `SHA-256` pour les bundles d’applications.

  Ajouté dans Xamarin.Android 9.4.

- **AndroidApkSignerAdditionalArguments** &ndash; propriété de chaîne qui permet au développeur de transmettre des arguments supplémentaires à l’outil `apksigner`.

  Ajouté dans Xamarin.Android 8.2.

- **AndroidApkSigningAlgorithm** &ndash; valeur de chaîne qui spécifie l’algorithme de signature à utiliser avec `jarsigner -sigalg`.

  La valeur par défaut est `md5withRSA` pour les fichiers APK, et `SHA256withRSA` pour les bundles d’applications.

  Ajouté dans Xamarin.Android 8.2.

- **AndroidApplication** &ndash; valeur booléenne qui indique si le projet est pour une application Android (`True`) ou pour un projet de bibliothèque Android (`False` ou absente).

  Un seul projet avec `<AndroidApplication>True</AndroidApplication>` peut être présent dans un package Android. (Malheureusement, ceci n’a pas encore été implémenté, ce qui peut entraîner des erreurs subtiles et inattendues concernant des ressources Android.)

- **AndroidApplicationJavaClass** &ndash; nom de classe Java complet à utiliser à la place de `android.app.Application` quand une classe hérite de [Android.App.Application](xref:Android.App.Application).

  Cette propriété est généralement définie par *d’autres* propriétés, comme la propriété MSBuild `$(AndroidEnableMultiDex)`.

  Ajouté dans Xamarin.Android 6.1.

- **AndroidBuildApplicationPackage** &ndash; valeur booléenne qui indique s’il faut créer et signer le package (.apk). Définir cette valeur sur `True` équivaut à utiliser la cible de build [SignAndroidPackage](#Build_Targets).

  La prise en charge de cette propriété a été ajoutée après Xamarin.Android 7.1.

  Cette propriété est définie par défaut sur `False`.

- **AndroidDexTool** &ndash; Propriété de style d’énumération qui peut avoir `dx` ou `d8` comme valeur. Indique le compilateur [dex][dex] Android utilisé pendant le processus de génération de Xamarin.Android.
  La valeur par défaut est `dx`. Pour plus d’informations, consultez notre documentation sur [D8 et R8][d8-r8].

  [dex]: https://source.android.com/devices/tech/dalvik/dalvik-bytecode
  [d8-r8]: https://github.com/xamarin/xamarin-android/blob/master/Documentation/guides/D8andR8.md

- **AndroidEnableDesugar** &ndash;Propriété booléenne qui détermine si `desugar` est activé. Android ne prend pas en charge toutes les fonctionnalités Java 8, et la chaîne d’outils par défaut implémente les nouvelles fonctionnalités de langage en effectuant des transformations de bytecode, appelées `desugar`, sur la sortie du compilateur `javac`. La valeur par défaut est `False` si vous utilisez `AndroidDexTool=dx`, ou `True` si vous utilisez `AndroidDexTool=d8`.

- **AndroidEnableGooglePlayStoreChecks** &ndash; une propriété bool qui permet aux développeurs de désactiver les vérifications de Google Play Store suivantes : XA1004, XA1005 et XA1006. Elle est utile pour les développeurs qui ne ciblent pas le Google Play Store et ne souhaitent pas exécuter ces vérifications.

  Ajouté dans Xamarin.Android 9.4.

- **AndroidEnableMultiDex** &ndash; propriété booléenne qui détermine si la prise en charge de Multi-Dex doit être utilisée dans le fichier `.apk` final.

  La prise en charge de cette propriété a été ajoutée dans Xamarin.Android 5.1.

  Cette propriété est définie par défaut sur `False`.

- **AndroidEnablePreloadAssemblies** &ndash; : propriété booléenne qui contrôle si tous les assemblys gérés fournis dans le package d’application sont chargés ou non lors du démarrage du processus.

  Avec la valeur `True`, tous les assemblys fournis dans le package d’application seront chargés lors du démarrage du processus, avant l’appel de tout code d’application.
  Ce processus est similaire à ce que faisait Xamarin.Android dans les versions antérieures à Xamarin.Android 9.2.

  Avec la valeur `False`, les assemblys seront uniquement chargés selon les besoins.
  Ce processus permet aux applications de démarrer plus rapidement et correspond davantage à la sémantique .NET.  Pour visualiser le gain de temps obtenu, définissez la propriété système `debug.mono.log` pour inclure `timing`, puis recherchez le message `Finished loading assemblies: preloaded` dans `adb logcat`.

  Pour les applications ou bibliothèques qui utilisent l’injection de dépendances, cette propriété *devra* éventuellement être définie sur `True` si elles nécessitent en retour que `AppDomain.CurrentDomain.GetAssemblies()` renvoie tous les assemblys dans le bundle d’application, même si l’assembly n’était pas nécessaire.

  Par défaut, cette valeur sera définie sur `True`.

  Ajouté dans Xamarin.Android 9.2.

- **AndroidEnableProfiledAot** &ndash; Propriété booléenne qui détermine si les profils AOT sont utilisés lors de la compilation Ahead Of Time.

  Les profils sont listés dans le groupe d’éléments `AndroidAotProfile`. Ce groupe d’éléments contient le ou les profils par défaut. Vous pouvez les remplacer en supprimant les profils existants et en ajoutant vos propres profils AOT.

  La prise en charge de cette propriété a été ajoutée dans Xamarin.Android 9.4.

  Cette propriété est définie par défaut sur `False`.

- **AndroidEnableSGenConcurrent** &ndash; propriété booléenne qui détermine si le [collecteur GC simultané](https://www.mono-project.com/docs/about-mono/releases/4.8.0/#concurrent-sgen) de Mono doit ou non être utilisé.

  La prise en charge de cette propriété a été ajoutée dans Xamarin.Android 7.2.

  Cette propriété est définie par défaut sur `False`.

- **AndroidErrorOnCustomJavaObject** &ndash; propriété booléenne qui détermine si les types peuvent implémenter `Android.Runtime.IJavaObject`
   *sans* hériter également de `Java.Lang.Object` ou de `Java.Lang.Throwable` :

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

- **AndroidFastDeploymentType** &ndash; Liste de valeurs séparées par le signe deux-points (`:`) pour contrôler les types qui peuvent être déployés sur le [répertoire de déploiement rapide](#Fast_Deployment) de l’appareil cible quand la propriété MSBuild `$(EmbedAssembliesIntoApk)` est définie sur `False`. Si une ressource est déployée via le déploiement rapide, elle n’est *pas* incorporée dans le fichier `.apk` généré, ce qui peut accélérer les temps de déploiement. (Plus il s’agit d’un déploiement rapide, moins le `.apk` doit être régénéré, et le processus d’installation peut être plus rapide.) Les valeurs valides sont les suivantes :

  - `Assemblies` : déployer les assemblys de l’application.

  - `Dexes` : déployer les fichiers `.dex`, les ressources Android et les composants Android. **Cette valeur peut être utilisée *uniquement* sur les appareils exécutant Android 4.4 ou ultérieur (API-19).**

  La valeur par défaut est `Assemblies`.

  **Experimental**. Ajouté dans Xamarin.Android 6.1.

- **AndroidGenerateJniMarshalMethods** &ndash; Propriété booléenne qui permet de générer des méthodes de marshaling JNI dans le cadre du processus de génération. Cela réduit considérablement l’utilisation de System.Reflection dans le code d’assistance de liaison.

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

- **AndroidGenerateJniMarshalMethodsAdditionalArguments** &ndash; Propriété de chaîne qui permet d’ajouter des paramètres à l’appel `jnimarshalmethod-gen.exe`.  Cette propriété est utile pour le débogage et rendre possible l’utilisation d’options telles que `-v`, `-d`, ou `--keeptemp`.

  La valeur par défaut est une chaîne vide. Elle peut être définie dans le fichier .csproj ou sur la ligne de commande. Exemple :

  ```xml
  <AndroidGenerateJniMarshalMethodsAdditionalArguments>-v -d --keeptemp</AndroidGenerateJniMarshalMethodsAdditionalArguments>
  ```

  ou :

  ```
  /p:AndroidGenerateJniMarshalMethodsAdditionalArguments="-v -d --keeptemp"
  ```

  Ajouté dans Xamarin.Android 9.2.

- **AndroidHttpClientHandlerType** &ndash; contrôle l’implémentation `System.Net.Http.HttpMessageHandler` par défaut qui sera utilisée par le constructeur `System.Net.Http.HttpClient` par défaut. Sa valeur est un nom de type qualifié d’assembly d’une sous-classe `HttpMessageHandler`, utilisable avec [`System.Type.GetType(string)`](https://docs.microsoft.com/dotnet/api/system.type.gettype?view=netcore-2.0#System_Type_GetType_System_String_).
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

- **AndroidLinkMode** &ndash; spécifie le type de [liaison](~/android/deploy-test/linker.md) à effectuer sur les assemblys contenus dans le package Android. Utilisé seulement dans les projets d’application Android. La valeur par défaut est *SdkOnly*. Les valeurs valides sont :

  - **None** : aucune liaison n’est tentée.

  - **SdkOnly** : la liaison est effectuée seulement sur les bibliothèques de classes de base, pas sur les assemblys de l’utilisateur.

  - **Full** : la liaison est effectuée seulement sur les bibliothèques de classes de base et sur les assemblys de l’utilisateur.

    > [!NOTE]
    > L’utilisation de la valeur `AndroidLinkMode`Full*pour* aboutit généralement à des applications endommagées, en particulier quand la réflexion est utilisée. À éviter, sauf si vous savez *vraiment* ce que vous faites.

  ```xml
  <AndroidLinkMode>SdkOnly</AndroidLinkMode>
  ```

- **AndroidLinkSkip** &ndash; spécifie une liste (séparée par des points-virgules [`;`]) des noms d’assembly, sans les extensions de fichier, des assemblys qui ne doivent pas être liés. Utilisé seulement dans les projets d’application Android.

  ```xml
  <AndroidLinkSkip>Assembly1;Assembly2</AndroidLinkSkip>
  ```

- **AndroidLinkTool** &ndash; Propriété de style d’énumération qui peut avoir `proguard` ou `r8` comme valeur. Indique le réducteur de code utilisé pour le code Java. La valeur par défaut est une chaîne vide, ou `proguard` si `$(AndroidEnableProguard)` est `True`. Pour plus d’informations, consultez notre documentation sur [D8 et R8][d8-r8].

  [d8-r8]: https://github.com/xamarin/xamarin-android/blob/master/Documentation/guides/D8andR8.md

- **AndroidLintEnabled** &ndash; Propriété booléenne qui permet au développeur d’exécuter l’outil `lint` android dans le cadre du processus d’empaquetage.

  - **AndroidLintEnabledIssues** &ndash; Liste séparée par des virgules des problèmes lint à activer.

  - **AndroidLintDisabledIssues** &ndash; Liste séparée par des virgules des problèmes lint à désactiver.

  - **AndroidLintCheckIssues** &ndash; Liste séparée par des virgules des problèmes lint à vérifier.
    Remarque : Seuls ces problèmes sont vérifiés.

  - **AndroidLintConfig** &ndash; Action de génération pour un fichier de configuration de style lint. Cela peut servir à activer/désactiver les problèmes à vérifier. Plusieurs fichiers dont le contenu est appelé à être fusionné peuvent utiliser cette action de génération.

  Consultez l’[aide de Lint](https://developer.android.com/studio/write/lint) pour plus d’informations sur l’outil `lint` android.

- **AndroidManagedSymbols** &ndash; propriété booléenne qui contrôle si des points de séquence sont générés, pour permettre l’extraction des informations de nom de fichier et de numéro de ligne dans les traces de la pile `Release`.

  Ajouté dans Xamarin.Android 6.1.

- **AndroidManifest** &ndash; spécifie un nom de fichier à utiliser comme modèle pour le fichier [ `AndroidManifest.xml` ](~/android/platform/android-manifest.md) de l’application.
  Lors de la génération, toutes les autres valeurs nécessaires y sont fusionnées pour produire le fichier `AndroidManifest.xml` effectif.
  `$(AndroidManifest)` doit contenir le nom du package dans l’attribut `/manifest/@package`.

- **AndroidMultiDexClassListExtraArgs** &ndash; Propriété de chaîne qui permet aux développeurs de passer des arguments supplémentaires à `com.android.multidex.MainDexListBuilder` durant la génération du fichier `multidex.keep`.

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

- **AndroidPackageFormat** &ndash; Propriété d’énumération dont les valeurs valides sont `apk` ou `aab`. Cela indique si vous souhaitez empaqueter l’application Android sous la forme d’un [fichier APK][apk] ou d’un [bundle d’applications Android][bundle]. Les bundles d’applications sont un nouveau format pour les builds `Release` destinées à être envoyées à Google Play. La valeur par défaut est `apk`.

  Lorsque `$(AndroidPackageFormat)` est défini sur `aab`, d’autres propriétés MSBuild sont définies. Celles-ci sont nécessaires pour les bundles d’applications Android :

  - `$(AndroidUseAapt2)` est `True`.
  - `$(AndroidUseApkSigner)` est `False`.
  - `$(AndroidCreatePackagePerAbi)` est `False`.

  [apk]: https://en.wikipedia.org/wiki/Android_application_package
  [bundle]: https://developer.android.com/platform/technology/app-bundle

- **AndroidR8JarPath** &ndash; Chemin de `r8.jar` à utiliser avec le réducteur et le compilateur Dex r8. Correspond par défaut à un chemin dans l’installation de Xamarin.Android. Pour plus d’informations, consultez notre documentation sur [D8 et R8][d8-r8].

- **AndroidSdkBuildToolsVersion** &ndash; le package d’outils de génération du SDK Android fournit entre autres les outils **aapt** et **zipalign**. Plusieurs versions différentes du package d’outils de génération peuvent être installées en parallèle. Le package d’outils de génération choisi pour l’empaquetage est réalisé en recherchant et en utilisant, le cas échéant, une version « favorite » des outils de génération ; s’il n’en existe *pas*, le package d’outils de génération associé à la version la plus récente sera utilisé.

  La propriété MSBuild `$(AndroidSdkBuildToolsVersion)` contient la version préférée des outils de génération. Le système de génération de Xamarin.Android fournit une valeur par défaut dans `Xamarin.Android.Common.targets`, et vous pouvez remplacer la valeur par défaut dans votre fichier projet et choisir une autre version des outils de génération, si (par exemple) la version la plus récente de aapt plante, alors qu’une version antérieure de aapt est connue pour fonctionner.

- **AndroidSupportedAbis** &ndash; propriété de type chaîne qui contient une liste d’ABI (avec un point-virgule [`;`] comme séparateur) qui doivent être inclus dans le fichier `.apk`.

  Les valeurs prises en charge sont :

  - `armeabi-v7a`
  - `x86`
  - `arm64-v8a` : nécessite Xamarin.Android 5.1 et ultérieur.
  - `x86_64` : nécessite Xamarin.Android 5.1 et ultérieur.

- **AndroidTlsProvider** &ndash; valeur de chaîne qui spécifie le fournisseur TLS qui doit être utilisé dans une application. Les valeurs possibles sont :

  - Unset/la chaîne vide : dans Xamarin. Android 7,3 et versions ultérieures, cela équivaut à `btls`.

    dans Xamarin.Android 7.1, équivaut à `legacy`.

    Cela correspond au paramètre **Par défaut** dans les pages de propriétés de Visual Studio.

  - `btls` : utilise [BoringSSL](https://boringssl.googlesource.com/boringssl) pour la communication TLS avec [HttpWebRequest](xref:System.Net.HttpWebRequest).

    Cela permet d’utiliser TLS 1.2 sur toutes les versions d’Android.

    Cela correspond au paramètre **TLS 1.2+ natif** dans les pages de propriétés de Visual Studio.

  - `legacy` : utilise l’implémentation historique gérée de SSL pour l’interaction réseau. Ceci *ne prend pas* en charge TLS 1.2.

    Cela correspond au paramètre **TLS 1.0 managé** dans les pages de propriétés de Visual Studio.

  - `default`: cette valeur est peu susceptible d’être utilisée dans les projets Xamarin. Android. Dans ce cas, la valeur recommandée est une chaîne vide, qui correspond au paramètre **Par défaut** des pages de propriétés Visual Studio.

    La valeur `default` n’est pas disponible dans les pages de propriétés Visual Studio.

    Ceci équivaut à `legacy`.

  Ajouté dans Xamarin.Android 7.1.

- **AndroidUseApkSigner** &ndash; propriété booléenne qui permet au développeur d’utiliser l’outil `apksigner` plutôt que `jarsigner`.

    Ajouté dans Xamarin.Android 8.2.

- **AndroidUseLegacyVersionCode** &ndash; propriété booléenne qui permet au développeur de rétablir le comportement du calcul versionCode d’avant Xamarin.Android 8.2. Elle est RÉSERVÉE aux développeurs possédant des applications dans Google Play Store. Il est fortement recommandé d’utiliser la nouvelle propriété `$(AndroidVersionCodePattern)`.

  Ajouté dans Xamarin.Android 8.2.

- **AndroidUseManagedDesignTimeResourceGenerator** &ndash; propriété booléenne qui change la génération au moment de la conception pour utiliser l’analyseur de ressources managées plutôt que `aapt`.

  Ajouté dans Xamarin.Android 8.1.

- **AndroidUseSharedRuntime** &ndash; propriété booléenne qui détermine si les *packages de runtime partagé* sont nécessaires pour exécuter l’application sur l’appareil cible. Le fait de s’appuyer sur les packages de runtime partagé permet de réduire la taille du package d’application, ce qui accélère le processus de création et de déploiement du package, aboutissant à un cycle de génération/déploiement/débogage plus rapide.

  Cette propriété doit être définie sur `True` pour les versions Debug, et sur `False` pour les projets Release.

- **AndroidVersionCodePattern** &ndash; propriété de type chaîne qui permet au développeur de personnaliser `versionCode` dans le manifeste.
  Consultez [Création de code de version pour APK](~/android/deploy-test/building-apps/abi-specific-apks.md) pour plus d’informations sur le choix d’un `versionCode`.

  Voici des exemples : si `abi` est `armeabi` et si `versionCode` dans le manifeste est `123`, `{abi}{versionCode}` produit un code de version `1123` quand `$(AndroidCreatePackagePerAbi)` a la valeur True ; sinon il produit la valeur 123.
  Si `abi` est `x86_64` et `versionCode` dans le manifeste est `44`. Ceci produit `544` quand `$(AndroidCreatePackagePerAbi)` est True ; sinon, il produit la valeur `44`.

  Si nous incluons une chaîne avec remplissage à gauche `{abi}{versionCode:0000}`, ceci produit `50044`, car nous remplissons le `versionCode` à gauche avec `0`. Vous pouvez également utiliser le remplissage décimal tel que `{abi}{versionCode:D4}`
  qui fait la même chose que l’exemple précédent.

  Seules les chaînes avec le format de remplissage « 0 » et « Dx » sont prises en charge, car la valeur DOIT être un entier.

  Éléments clés prédéfinis

  - **abi**  &ndash; insère l’ABI ciblé pour l’application
    - 2 &ndash; `armeabi-v7a`
    - 3 &ndash; `x86`
    - 4 &ndash; `arm64-v8a`
    - 5 &ndash; `x86_64`

  - **minSDK**  &ndash; insère la valeur minimale du SDK prise en charge à partir de `AndroidManifest.xml` ou `11` si aucune valeur n’est définie.

  - **versionCode** &ndash; utilise le code de version provenant directement de `Properties\AndroidManifest.xml`.

  Vous pouvez définir des éléments personnalisés en utilisant la propriété `$(AndroidVersionCodeProperties)` (définie ensuite).

  La valeur par défaut est `{abi}{versionCode:D6}`. Si un développeur souhaite conserver l’ancien comportement, vous pouvez la remplacer en définissant la propriété `$(AndroidUseLegacyVersionCode)` sur `true`.

  Ajouté dans Xamarin.Android 7.2.

- **AndroidVersionCodeProperties** &ndash; Propriété de type chaîne qui permet aux développeurs de définir des éléments personnalisés à utiliser avec `AndroidVersionCodePattern`. Ils sont sous la forme d’une paire `key=value`. Tous les éléments dans `value` doivent être des valeurs entières. Par exemple : `screen=23;target=$(_AndroidApiLevel)`. Vous constatez que vous pouvez utiliser des propriétés MSBuild existantes ou personnalisées dans la chaîne.

  Ajouté dans Xamarin.Android 7.2.

- **AotAssemblies** &ndash; propriété booléenne qui détermine si les assemblys doivent ou non être compilés Ahead of Time en code natif et inclus dans le fichier `.apk`.

  La prise en charge de cette propriété a été ajoutée dans Xamarin.Android 5.1.

  Cette propriété est définie par défaut sur `False`.

- **EmbedAssembliesIntoApk** &ndash; propriété booléenne qui détermine si les assemblys de l’application doivent ou non être incorporés dans le package d’application.

  Cette propriété doit être définie sur `True` pour les versions Release, et sur `False` pour les projets Debug. Sa valeur *doit* être `True` dans les versions Debug si le déploiement rapide ne prend pas en charge l’appareil cible.

  Quand cette propriété est `False`, la propriété MSBuild `$(AndroidFastDeploymentType)` détermine également ce qui doit être incorporé dans le fichier `.apk`, ce qui peut impacter les temps de déploiement et de regénération.

- **EnableLLVM** &ndash; propriété booléenne qui détermine si LLVM doit ou non être utilisé quand la compilation Ahead of Time génère des assemblys en code natif.

  La prise en charge de cette propriété a été ajoutée dans Xamarin.Android 5.1.

  Cette propriété est définie par défaut sur `False`.

  Cette propriété est ignorée sauf si la propriété MSBuild `$(AotAssemblies)` est définie sur `True`.

- **EnableProguard** &ndash; propriété booléenne qui détermine si [proguard](https://developer.android.com/tools/help/proguard.html) est exécuté dans le cadre du processus de packaging pour lier le code Java.

  La prise en charge de cette propriété a été ajoutée dans Xamarin.Android 5.1.

  Cette propriété est définie par défaut sur `False`.

  Quand elle a la valeur `True`, les fichiers [ProguardConfiguration](#ProguardConfiguration) sont utilisés pour contrôler l’exécution de `proguard`.

- **JavaMaximumHeapSize** &ndash; spécifie la valeur du paramètre **java**
  `-Xmx` à utiliser lors de la génération du fichier `.dex` dans le cadre du processus de packaging. Si non spécifié, l’option `-Xmx` fournit **java** avec la valeur `1G`. Ceci est couramment requis sur Windows par rapport à d’autres plateformes.

  La spécification de cette propriété est nécessaire si la cible [`_CompileDex` lève une erreur `java.lang.OutOfMemoryError`](https://bugzilla.xamarin.com/show_bug.cgi?id=18327).

  Personnalisez la valeur en changeant :

  ```xml
  <JavaMaximumHeapSize>1G</JavaMaximumHeapSize>
  ```

- **JavaOptions** &ndash; spécifie des options de ligne de commande supplémentaires à passer à **java** lors de la génération du fichier `.dex`.

- **LinkerDumpDependencies** &ndash; Propriété booléenne qui active la génération du fichier de dépendances de l’éditeur de liens. Ce fichier peut être utilisé en tant qu’entrée pour l’outil [illinkanalyzer](https://github.com/mono/linker/blob/master/src/analyzer/README.md).

  La valeur par défaut est False.

- **MandroidI18n** &ndash; spécifie la prise en charge de l’internationalisation incluse avec l’application, comme le classement et tri des tables. La valeur est une liste (délimitée par des virgules ou des points-virgules) d’une ou plusieurs des valeurs suivantes, qui ne respectent pas la casse :

  - **None** : n’inclure aucun encodage supplémentaire.

  - **All** : inclure tous les encodages disponibles.

  - **CJK** : incluent les encodages du chinois, du japonais et du coréen, comme *Japonais (EUC)* \[enc-jp, CP51932\], *Japonais (Shift-JIS)* \[ ISO-2022-jp, shift\_jis, CP932\], *Japonais (JIS)* \[CP50220\], *Chinois simplifié (GB2312)* \[gb2312, CP936\], *Coréen (UHC)* \[ks\_c\_5601-1987, CP949\], *Coréen (EUC)* \[euc-kr, CP51949\], *Chinois traditionnel (Big5)* \[big5, CP950\] et *Chinois simplifié (GB18030)* \[ GB18030, CP54936\].

  - **MidEast** : inclure les encodages du Moyen-Orient, comme *Turc (Windows)* \[iso-8859-9, CP1254\], *Hébreu (Windows)* \[windows-1255, CP1255\], *Arabe (Windows)* \[windows-1256, CP1256\], *Arabe (ISO)* \[iso-8859-6, CP28596\], *Hébreu (ISO)* \[iso-8859-8, CP28598\], *Latin 5 (ISO)* \[iso-8859-9, CP28599\] et *Hébreu (Iso Alternative)* \[iso-8859-8, CP38598\].

  - **Other** : inclure d’autres encodages, comme *Cyrillique (Windows)* \[CP1251\], *Balte (Windows)* \[iso-8859-4, CP1257\], *Vietnamien (Windows)* \[CP1258\], *Cyrillique (KOI8-R)* \[koi8-r, CP1251\], *Ukrainien (KOI8-U)* \[koi8-u, CP1251\], *Balte (ISO)* \[iso-8859-4, CP1257\], *Cyrillique (ISO)* \[iso-8859-5, CP1251\], *ISCII Devanagari* \[x-iscii-de, CP57002\], *Bengali ISCII* \[x-iscii-be, CP57003\], *Tamoul ISCII* \[x-iscii-ta, CP57004\], *Télougou ISCII* \[x-iscii-te, CP57005\], *Assamais ISCII* \[x-iscii-as, CP57006\], *Oriya ISCII* \[x-iscii-or, CP57007\], *Kannada ISCII* \[x-iscii-ka, CP57008\], *Malayalam ISCII* \[x-iscii-ma, CP57009\], *Gujarati ISCII* \[x-iscii-gu, CP57010\], *Punjabi ISCII* \[x-iscii-pa, CP57011\] et *Thaï (Windows)* \[CP874\].

  - **Rare** : inclure des encodages rares, comme *IBM EBCDIC (Turc)* \[CP1026\], *IBM EBCDIC (Systèmes ouverts Latin 1)* \[CP1047\], *IBM EBCDIC (É.U. - Canada avec Euro)* \[CP1140\], *IBM EBCDIC (Allemagne avec Euro)* \[CP1141\], *IBM EBCDIC (Danemark/Norvège avec Euro)* \[CP1142\], *IBM EBCDIC (Finlande/Suède avec Euro)* \[CP1143\], *IBM EBCDIC (Italie avec Euro)* \[CP1144\], *IBM EBCDIC (Amérique latine/Espagne avec Euro)* \[CP1145\], *IBM EBCDIC (Royaume-Uni avec Euro)* \[CP1146\], *IBM EBCDIC (France avec Euro)* \[CP1147\], *IBM EBCDIC (International avec Euro)* \[CP1148\], *IBM EBCDIC (Islandais avec Euro)* \[CP1149\], *IBM EBCDIC (Allemagne)* \[CP20273\], *IBM EBCDIC (Danemark/Norvège)* \[CP20277\], *IBM EBCDIC (Finlande/Suède)* \[CP20278\], *IBM EBCDIC (Italie)* \[CP20280\], *IBM EBCDIC (Amérique latine/Espagne)* \[CP20284\], *IBM EBCDIC (Royaume-Uni)* \[CP20285\], *IBM EBCDIC (Japonais Katakana étendu)* \[CP20290\], *IBM EBCDIC (France)* \[CP20297\], *IBM EBCDIC (Arabe)* \[CP20420\], *IBM EBCDIC (Hébreu)* \[CP20424\], *IBM EBCDIC (Islandais)* \[CP20871\], *IBM EBCDIC (Cyrillique - Serbe, Bulgare)* \[CP21025\], *IBM EBCDIC (É.U. - Canada)* \[CP37\], *IBM EBCDIC (International)* \[CP500\], *Arabe (ASMO 708)* \[CP708\], *Europe centrale (DOS)* \[CP852\] *, Cyrillique (DOS)* \[CP855\], *Turc (DOS)* \[CP857\], *Europe de l’Ouest (DOS avec Euro)* \[CP858\], *Hébreu (DOS)* \[CP862\], *Arabe (DOS)* \[CP864\], *Russe (DOS)* \[CP866\], *Grec (DOS)* \[CP869\], *IBM EBCDIC (Latin 2)* \[CP870\] et *IBM EBCDIC (Grec)* \[CP875\].

  - **West** : inclure les encodages occidentaux, comme *Europe de l’Ouest (Mac)* \[macintosh, CP10000\], *Islandais (Mac)* \[x-mac-icelandic, CP10079\], *Europe centrale (Windows)* \[iso-8859-2, CP1250\], *Europe centrale (Windows)* \[iso-8859-1, CP1252\], *Grec (Windows)* \[iso-8859-7, CP1253\], *Europe centrale (ISO)* \[iso-8859-2, CP28592\], *Latin 3 (ISO)* \[iso-8859-3, CP28593\], *Grec (ISO)* \[iso-8859-7, CP28597\], *Latin 9 (ISO)* \[iso-8859-15, CP28605\], *OEM États-Unis* \[CP437\], *Europe de l’Ouest (DOS)* \[CP850\], *Portugais (DOS)* \[CP860\], *Islandais (DOS)* \[CP861\], *Français (Canada) (DOS)* \[CP863\] et *Nordique (DOS)* \[CP865\].

  ```xml
  <MandroidI18n>West</MandroidI18n>
  ```

- **MonoSymbolArchive** &ndash; propriété booléenne qui contrôle si les artefacts `.mSYM` sont créés pour une utilisation ultérieure avec `mono-symbolicate`, pour extraire les informations de nom de fichier et de numéro de ligne &ldquo;réelles&rdquo; des traces de pile Release.

  Elle a la valeur True par défaut pour les applications &ldquo;Release&rdquo; pour lesquelles les symboles de débogage sont activés : `$(EmbedAssembliesIntoApk)` a la valeur True, `$(DebugSymbols)` a la valeur True et `$(Optimize)` a la valeur True.

  Ajouté dans Xamarin.Android 7.1.

### <a name="binding-project-build-properties"></a>Liaison des propriétés de build du projet

Les propriétés MSBuild suivantes sont utilisées avec les [projets de liaison](~/android/platform/binding-java-library/index.md) :

- **AndroidClassParser** &ndash; propriété de type chaîne qui contrôle comment les fichiers `.jar` sont analysés. Les valeurs possibles incluent notamment :

  - **class-parse** : utilise `class-parse.exe` pour analyser le bytecode Java directement, sans l’aide d’une machine virtuelle Java. Cette valeur est expérimentale.

  - **jar2xml** : utilisez `jar2xml.jar` pour utiliser la réflexion Java pour extraire des types et des membres de réflexion d’un fichier `.jar`.

  Les avantages de `class-parse` par rapport à `jar2xml` sont :

  - `class-parse` peut extraire des noms de paramètre du bytecode Java qui contient des symboles de *débogage* (par exemple du bytecode compilé avec `javac -g`).

  - `class-parse` « n’ignore pas » les classes qui héritent de ou qui contiennent des membres de types qui ne peuvent pas être résolus.

  **Experimental**. Ajouté dans Xamarin.Android 6.0.

  La valeur par défaut est `jar2xml`.

  La valeur par défaut changera dans une version ultérieure.

- **AndroidCodegenTarget** &ndash; propriété de chaîne qui contrôle l’ABI cible de la génération de code. Les valeurs possibles incluent notamment :

  - **XamarinAndroid** : utilise l’API de liaison JNI présente depuis Mono pour Android 1.0. La liaison des assemblys générés avec Xamarin.Android 5.0 ou ultérieur peut s’exécuter seulement sur Xamarin.Android 5.0 ou ultérieur (ajouts d’API/ABI), mais la *source* est compatible avec les versions antérieures du produit.

  - **XAJavaInterop1** : utilise Java.Interop pour les appels JNI. La liaison des assemblys avec `XAJavaInterop1` peut être générée et s’exécuter seulement avec Xamarin.Android 6.1 ou ultérieur. Xamarin.Android 6.1 et ultérieur effectue la liaison de `Mono.Android.dll` avec cette valeur.

    Les avantages de `XAJavaInterop1` sont :

    - Assemblys plus petits.

    - Mise en cache de `jmethodID` pour les appels de méthode `base`, dès lors que tous les autres types de liaison de la hiérarchie d’héritage sont générés avec `XAJavaInterop1` ou ultérieur.

    - Mise en cache de `jmethodID` pour les constructeurs de wrapper appelables par Java pour les sous-classes managées.

    La valeur par défaut est `XAJavaInterop1`.

### <a name="resource-properties"></a>Propriétés de la ressource

Les propriétés des ressources contrôlent la génération du fichier `Resource.designer.cs`, qui permet d’accéder aux ressources Android.

- **AndroidAapt2CompileExtraArgs** &ndash; Spécifie des options de ligne de commande supplémentaires à passer à la commande **aapt2 compile** pendant le traitement des composants et des ressources Android.

  Ajouté dans Xamarin.Android 9.1.

- **AndroidAapt2LinkExtraArgs** &ndash; Spécifie des options de ligne de commande supplémentaires à passer à la commande **aapt2 link** pendant le traitement des composants et des ressources Android.

  Ajouté dans Xamarin.Android 9.1.

- **AndroidExplicitCrunch** &ndash; si vous générez une application avec un très grand nombre de drawables locaux, une build initiale (ou une regénération) peut durer plusieurs minutes. Pour accélérer le processus de génération, essayez en incluant cette propriété et en la définissant sur `True`. Quand cette propriété est définie, le processus de génération compresse les fichiers .png.

  Remarque : Cette option n’est pas compatible avec l’option `$(AndroidUseAapt2)`. Si `$(AndroidUseAapt2)` est activé, cette fonctionnalité est désactivée. Si vous souhaitez continuer à utiliser cette fonctionnalité, définissez `$(AndroidUseAapt2)` sur `False`.

  **Experimental**. Ajouté dans Xamarin.Android 7.0.

- **AndroidResgenExtraArgs** &ndash; spécifie des options de ligne de commande supplémentaires à passer à la commande **aapt** lors du traitement des composants et des ressources Android.

- **AndroidResgenFile** &ndash; spécifie le nom du fichier de ressources à générer. Le modèle par défaut définit cette valeur sur `Resource.designer.cs`.

- **AndroidUseAapt2** &ndash; Propriété booléenne qui permet au développeur de contrôler l’utilisation de l’outil `aapt2` pour l’empaquetage.
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

- **MonoAndroidResourcePrefix** &ndash; spécifie un *préfixe de chemin* qui est supprimé du début des noms de fichiers avec une action de génération de `AndroidResource`. Ceci permet de changer l’emplacement où se trouvent les ressources.

  La valeur par défaut est `Resources`. Changez ceci en `res` pour la structure de projet Java.

<a name="Signing_Properties" />

### <a name="signing-properties"></a>Propriétés de signature

Les propriétés de signature contrôlent comment le package d’application est signé pour qu’il puisse être installé sur un appareil Android. Pour permettre une itération plus rapide des générations, les tâches Xamarin.Android ne signent pas les packages pendant le processus de génération, car l’opération de signature est assez lente. Au lieu de cela, les packages sont signés (si nécessaire) avant l’installation ou pendant l’exportation, par l’IDE ou par la cible de génération *Install*. Le fait d’appeler la cible *SignAndroidPackage* produit un package avec le suffixe `-Signed.apk` dans le répertoire de sortie.

Par défaut, la cible de signature génère si nécessaire une nouvelle clé de signature de débogage. Si vous voulez utiliser une clé spécifique, par exemple sur un serveur de builds, vous pouvez utiliser les propriétés MSBuild suivantes :

- **AndroidDebugKeyAlgorithm** &ndash; spécifie l’algorithme par défaut à utiliser pour le `debug.keystore`. Ce champ est défini sur `RSA` par défaut.

- **AndroidDebugKeyValidity** &ndash; spécifie la validité par défaut à utiliser pour le `debug.keystore`. Ce champ est défini sur `10950`, `30 * 365` ou `30 years` par défaut.

- **AndroidKeyStore** &ndash; une valeur booléenne qui indique si des informations de signature personnalisées doivent être utilisées. La valeur par défaut est `False`, qui signifie que la clé de signature de débogage par défaut est utilisée pour signer les packages.

- **AndroidSigningKeyAlias** &ndash; spécifie l’alias de la clé dans le magasin de clés. Il s’agit de la valeur **keytool -alias** utilisée lors de la création du magasin de clés.

- **AndroidSigningKeyPass** &ndash; spécifie le mot de passe de la clé dans le fichier du magasin de clés. C’est la valeur entrée quand `keytool` demande **Entrez le mot clé pour $(AndroidSigningKeyAlias)** .

- **AndroidSigningKeyStore** &ndash; spécifie le nom de fichier du fichier de magasin de clés créé par `keytool`. Ceci correspond à la valeur fournie pour l’option **keytool -keystore**.

- **AndroidSigningStorePass** &ndash; spécifie le mot de passe pour `$(AndroidSigningKeyStore)`. C’est la valeur fournie à `keytool` lors de la création du fichier de magasin de clés et demandée avec **Entrez le mot de passe du magasin de clés :** .

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

**Abi** &ndash; spécifie l’ABI de la bibliothèque native.

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

Les utilisateurs plus expérimentés souhaitent éventuellement avoir des ressources différentes utilisées dans des configurations différentes, mais avec le même chemin effectif. Il faut pour cela avoir plusieurs répertoires de ressources et des fichiers avec les mêmes chemins relatifs au sein de ces différents répertoires, et utiliser des conditions MSBuild pour inclure de façon conditionnelle des fichiers différents dans les différentes configurations. Exemple :

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

**LogicalName** &ndash; spécifie explicitement le chemin d’une ressource. Permet de &ldquo;définir des alias&rdquo; pour les fichiers, de sorte qu’ils soient disponibles sous plusieurs noms de ressource distincts.

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
