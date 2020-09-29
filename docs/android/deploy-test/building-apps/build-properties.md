---
title: Propriétés de build
description: Ce document répertorie toutes les propriétés prises en charge dans le processus de génération Xamarin. Android.
ms.prod: xamarin
ms.assetid: FC0DBC08-EBCB-4D2D-AB3F-76B54E635C22
ms.technology: xamarin-android
author: jonpryor
ms.author: jopryo
ms.date: 09/21/2020
ms.openlocfilehash: aeb0cca9ead1a0f0a3f5b1dec88b2470289cd589
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91454934"
---
# <a name="build-properties"></a>Propriétés de build

Les propriétés MSBuild contrôlent le comportement des [cibles](~/android/deploy-test/building-apps/build-targets.md).
Elles sont spécifiées dans le fichier projet, par exemple **MyApp. csproj**, au sein d’un [MSBuild PropertyGroup](/visualstudio/msbuild/propertygroup-element-msbuild).

## <a name="adbtarget"></a>AdbTarget

La `$(AdbTarget)` propriété spécifie l’appareil cible Android sur lequel le package Android peut être installé ou supprimé.
La valeur de cette propriété est identique à celle de l' [ `adb` option d’appareil cible](https://developer.android.com/tools/help/adb.html#issuingcommands).

## <a name="aftergenerateandroidmanifest"></a>AfterGenerateAndroidManifest

Les cibles MSBuild listées dans cette propriété sont exécutées directement après la `_GenerateJavaStubs` cible interne, où le `AndroidManifest.xml` fichier est généré dans le `$(IntermediateOutputPath)` . Si vous souhaitez apporter des modifications au `AndroidManifest.xml` fichier généré, vous pouvez le faire à l’aide de ce point d’extension.

Ajouté dans Xamarin.Android 9.4.

## <a name="androidaapt2compileextraargs"></a>AndroidAapt2CompileExtraArgs

Spécifie des options de ligne de commande supplémentaires à passer à la commande de **compilation aapt2** lors du traitement des ressources et ressources Android.

Ajouté dans Xamarin.Android 9.1.

## <a name="androidaapt2linkextraargs"></a>AndroidAapt2LinkExtraArgs

Spécifie des options de ligne de commande supplémentaires à passer à la commande **aapt2 Link** lors du traitement des ressources et ressources Android.

Ajouté dans Xamarin.Android 9.1.

## <a name="androidaotcustomprofilepath"></a>AndroidAotCustomProfilePath

Fichier qui `aprofutil` doit être créé pour contenir les données du profileur.

## <a name="androidaotprofiles"></a>AndroidAotProfiles

Propriété de type chaîne qui permet au développeur d’ajouter des profils AOA à partir de la ligne de commande. Il s’agit d’un point-virgule ou d’une liste de chemins absolus séparés par des virgules.
Ajouté dans Xamarin. Android 10,1.

## <a name="androidaotprofilerport"></a>AndroidAotProfilerPort

Port auquel `aprofutil` se connecter lors de l’obtention des données de profilage.

## <a name="androidapkdigestalgorithm"></a>AndroidApkDigestAlgorithm

Valeur de chaîne qui spécifie l’algorithme Digest à utiliser avec `jarsigner -digestalg` .

La valeur par défaut est `SHA-256`. Dans Xamarin. Android 10,0 et versions antérieures, la valeur par défaut était `SHA1` .

Ajouté dans Xamarin.Android 9.4.

## <a name="androidapksigneradditionalarguments"></a>AndroidApkSignerAdditionalArguments

Propriété de type chaîne qui permet au développeur de fournir des arguments supplémentaires à l' `apksigner` outil.

Ajouté dans Xamarin.Android 8.2.

## <a name="androidapksigningalgorithm"></a>AndroidApkSigningAlgorithm

Valeur de chaîne qui spécifie l’algorithme de signature à utiliser avec `jarsigner -sigalg` .

La valeur par défaut est `SHA256withRSA`. Dans Xamarin. Android 10,0 et versions antérieures, la valeur par défaut était `md5withRSA` .

Ajouté dans Xamarin.Android 8.2.

## <a name="androidapplication"></a>AndroidApplication

Valeur booléenne qui indique si le projet est destiné à une application Android ( `True` ) ou à un projet de bibliothèque Android ( `False` ou absent).

Un seul projet avec `<AndroidApplication>True</AndroidApplication>` peut être présent dans un package Android. (Malheureusement, ceci n’a pas encore été implémenté, ce qui peut entraîner des erreurs subtiles et inattendues concernant des ressources Android.)

## <a name="androidapplicationjavaclass"></a>AndroidApplicationJavaClass

Nom complet de la classe Java à utiliser à la place de `android.app.Application` lorsqu’une classe hérite d' [Android. app. application](xref:Android.App.Application).

Cette propriété est généralement définie par d' *autres* propriétés, telles que la `$(AndroidEnableMultiDex)` propriété MSBuild.

Ajouté dans Xamarin.Android 6.1.

## <a name="androidbinutilspath"></a>AndroidBinUtilsPath

Chemin d’accès à un répertoire contenant le [binutils][binutils] Android, tel que `ld` , l’éditeur de liens natif et `as` , l’assembleur natif. Ces outils font partie du NDK Android et sont également inclus dans l’installation de Xamarin. Android.

La valeur par défaut est `$(MonoAndroidBinDirectory)\ndk\`.

Ajouté dans Xamarin. Android 10,0.

[binutils]: https://android.googlesource.com/toolchain/binutils/

## <a name="androidboundexceptiontype"></a>AndroidBoundExceptionType

Valeur de chaîne qui spécifie comment les exceptions doivent être propagées lorsqu’un type fourni par Xamarin. Android implémente un type ou une interface .NET en termes de types Java, par exemple `Android.Runtime.InputStreamInvoker` et `System.IO.Stream` , ou `Android.Runtime.JavaDictionary` et `System.Collections.IDictionary` .

- `Java`: Le type d’exception Java d’origine est propagé tel quel.

  Cela signifie que, par exemple, `InputStreamInvoker` n’implémente pas correctement l' `System.IO.Stream` API, car `Java.IO.IOException` peut être levée à partir `Stream.Read()` de au lieu de `System.IO.IOException` .

  Il s’agit du comportement de propagation des exceptions dans toutes les versions de Xamarin. Android antérieures à 11,1.

  Il s’agit de la valeur par défaut dans Xamarin. Android 11,1.

- `System`: Le type d’exception Java d’origine est intercepté et encapsulé dans un type d’exception .NET approprié.

  Cela signifie que, par exemple, `InputStreamInvoker` implémente correctement `System.IO.Stream` et ne `Stream.Read()` lèvera *pas* d' `Java.IO.IOException` instances.  (Il peut lever `System.IO.IOException` à la place un qui a un `Java.IO.IOException` comme `Exception.InnerException` valeur.)

  Cela deviendra la valeur par défaut dans .NET 6,0.

Ajouté dans Xamarin. Android 10,2.

## <a name="androidbuildapplicationpackage"></a>AndroidBuildApplicationPackage

Valeur booléenne qui indique s’il faut créer et signer le package (. apk). La définition de cette valeur sur `True` équivaut à utiliser l’option [`SignAndroidPackage`](~/android/deploy-test/building-apps/build-targets.md#install)
cible de la Build.

La prise en charge de cette propriété a été ajoutée après Xamarin.Android 7.1.

Cette propriété est définie par défaut sur `False`.

## <a name="androidbundleconfigurationfile"></a>AndroidBundleConfigurationFile

Spécifie un nom de fichier à utiliser comme [fichier de configuration][bundle-config-format] lors de la `bundletool` création d’un bundle d’applications Android. Ce fichier contrôle certains aspects de la façon dont les fichiers APK sont générés à partir du regroupement, comme les dimensions que le bundle fractionne pour produire des fichiers APK. Notez que Xamarin. Android configure automatiquement certains de ces paramètres, y compris la liste des extensions de fichier à conserver non compressées.

Cette propriété est pertinente uniquement si [`$(AndroidPackageFormat)`](#androidpackageformat) a la valeur `aab` .

Ajouté dans Xamarin. Android 10,3.

[bundle-config-format]: https://developer.android.com/studio/build/building-cmdline#bundleconfig

## <a name="androidclassparser"></a>AndroidClassParser

Propriété de type chaîne qui contrôle la manière dont `.jar` les fichiers sont analysés. Les valeurs possibles incluent :

- **class-parse** : utilise `class-parse.exe` pour analyser le bytecode Java directement, sans l’aide d’une machine virtuelle Java. Cette valeur est expérimentale.

- **jar2xml** : utilisez `jar2xml.jar` pour utiliser la réflexion Java pour extraire des types et des membres de réflexion d’un fichier `.jar`.

Les avantages de `class-parse` par rapport à `jar2xml` sont :

- `class-parse` peut extraire des noms de paramètres du bytecode Java qui contient des symboles de *débogage* (bytecode compilé avec `javac -g` ).

- `class-parse` « n’ignore pas » les classes qui héritent de ou qui contiennent des membres de types qui ne peuvent pas être résolus.

**Expérimental**. Ajouté dans Xamarin.Android 6.0.

La valeur par défaut est `jar2xml`.

La prise en charge de `jar2xml` est obsolète et la prise en charge de `jar2xml` sera supprimée dans le cadre de .net 6.

## <a name="androidcodegentarget"></a>AndroidCodegenTarget

Propriété de type chaîne qui contrôle l’ABI cible de génération de code.
Les valeurs possibles incluent :

- **XamarinAndroid**: utilise l’API de liaison JNI présente dans la mesure où mono pour Android 1,0. La liaison des assemblys générés avec Xamarin.Android 5.0 ou ultérieur peut s’exécuter seulement sur Xamarin.Android 5.0 ou ultérieur (ajouts d’API/ABI), mais la *source* est compatible avec les versions antérieures du produit.

- **XAJavaInterop1** : utilise Java.Interop pour les appels JNI. La liaison des assemblys avec `XAJavaInterop1` peut être générée et s’exécuter seulement avec Xamarin.Android 6.1 ou ultérieur. Xamarin.Android 6.1 et ultérieur effectue la liaison de `Mono.Android.dll` avec cette valeur.

Les avantages de `XAJavaInterop1` sont :

- Assemblys plus petits.

- Mise en cache de `jmethodID` pour les appels de méthode `base`, dès lors que tous les autres types de liaison de la hiérarchie d’héritage sont générés avec `XAJavaInterop1` ou ultérieur.

- Mise en cache de `jmethodID` pour les constructeurs de wrapper appelables par Java pour les sous-classes managées.

La valeur par défaut est `XAJavaInterop1`.

## <a name="androidcreatepackageperabi"></a>AndroidCreatePackagePerAbi

Propriété booléenne qui détermine si un *ensemble* de fichiers--on par Abi spécifié dans [`$(AndroidSupportedAbis)`](#androidsupportedabis) --doit être créé au lieu d’avoir la prise en charge de tous les Abi dans un seul `.apk` .

Consultez également le Guide de fichiers APK pour la [création d’un guide spécifique Abi](~/android/deploy-test/building-apps/abi-specific-apks.md) .

## <a name="androiddebugkeyalgorithm"></a>AndroidDebugKeyAlgorithm

Spécifie l’algorithme par défaut à utiliser pour `debug.keystore` . Sa valeur par défaut est `RSA`.

## <a name="androiddebugkeyvalidity"></a>AndroidDebugKeyValidity

Spécifie la validité par défaut à utiliser pour `debug.keystore` . Ce champ est défini sur `10950`, `30 * 365` ou `30 years` par défaut.

## <a name="androiddebugstoretype"></a>AndroidDebugStoreType

Spécifie le format de fichier du magasin de clés à utiliser pour `debug.keystore` . Sa valeur par défaut est `pkcs12`.

Ajouté dans Xamarin. Android 10,2.

## <a name="androiddextool"></a>AndroidDexTool

Propriété de style enum avec des valeurs valides de `dx` ou `d8` . Indique le compilateur [dex][dex] Android utilisé pendant le processus de génération de Xamarin.Android.
La valeur par défaut est `dx`. Pour plus d’informations, consultez notre documentation sur [D8 et R8][d8-r8].

[dex]: https://source.android.com/devices/tech/dalvik/dalvik-bytecode
[d8-r8]: https://github.com/xamarin/xamarin-android/blob/master/Documentation/guides/D8andR8.md

## <a name="androidenabledesugar"></a>AndroidEnableDesugar

Propriété booléenne qui détermine si `desugar` est activé. Android ne prend pas en charge toutes les fonctionnalités Java 8, et la chaîne d’outils par défaut implémente les nouvelles fonctionnalités de langage en effectuant des transformations de bytecode, appelées `desugar`, sur la sortie du compilateur `javac`. La valeur par défaut est `False` si utilise `AndroidDexTool=dx` et a pour valeur par défaut `True` si utilise [`$(AndroidDexTool)`](#androiddextool) = `d8` .

## <a name="androidenablegoogleplaystorechecks"></a>AndroidEnableGooglePlayStoreChecks

Propriété bool qui permet aux développeurs de désactiver les vérifications de Google Play Store suivantes : XA1004, XA1005 et XA1006. Elle est utile pour les développeurs qui ne ciblent pas le Google Play Store et ne souhaitent pas exécuter ces vérifications.

Ajouté dans Xamarin.Android 9.4.

## <a name="androidenablemultidex"></a>AndroidEnableMultiDex

Propriété booléenne qui détermine si la prise en charge de multi-DEX sera utilisée dans la version finale `.apk` .

La prise en charge de cette propriété a été ajoutée dans Xamarin.Android 5.1.

Cette propriété est définie par défaut sur `False`.

## <a name="androidenablepreloadassemblies"></a>AndroidEnablePreloadAssemblies

Propriété booléenne qui contrôle si tous les assemblys managés regroupés dans le package d’application sont chargés au démarrage du processus ou non.

Avec la valeur `True`, tous les assemblys fournis dans le package d’application seront chargés lors du démarrage du processus, avant l’appel de tout code d’application.
Ce processus est similaire à ce que faisait Xamarin.Android dans les versions antérieures à Xamarin.Android 9.2.

Avec la valeur `False`, les assemblys seront uniquement chargés selon les besoins.
Ce processus permet aux applications de démarrer plus rapidement et correspond davantage à la sémantique .NET.  Pour visualiser le gain de temps obtenu, définissez la propriété système `debug.mono.log` pour inclure `timing`, puis recherchez le message `Finished loading assemblies: preloaded` dans `adb logcat`.

Pour les applications ou bibliothèques qui utilisent l’injection de dépendances, cette propriété *devra* éventuellement être définie sur `True` si elles nécessitent en retour que `AppDomain.CurrentDomain.GetAssemblies()` renvoie tous les assemblys dans le bundle d’application, même si l’assembly n’était pas nécessaire.

Par défaut, cette valeur sera définie sur `True`.

Ajouté dans Xamarin.Android 9.2.

## <a name="androidenableprofiledaot"></a>AndroidEnableProfiledAot

Propriété booléenne qui détermine si les profils AOA sont utilisés lors de la compilation anticipée.

Les profils sont répertoriés dans [`@(AndroidAotProfile)`](~/android/deploy-test/building-apps/build-items.md#androidaotprofile)
Groupe d’éléments. Ce groupe d’éléments contient le ou les profils par défaut. Vous pouvez la remplacer en supprimant les profils existants et en ajoutant vos propres profils AOA.

La prise en charge de cette propriété a été ajoutée dans Xamarin.Android 9.4.

Cette propriété est définie par défaut sur `False`.

## <a name="androidenablesgenconcurrent"></a>AndroidEnableSGenConcurrent

Propriété booléenne qui détermine si le [collecteur gc simultané](https://www.mono-project.com/docs/about-mono/releases/4.8.0/#concurrent-sgen) de mono doit ou non être utilisé.

La prise en charge de cette propriété a été ajoutée dans Xamarin.Android 7.2.

Cette propriété est définie par défaut sur `False`.

## <a name="androiderroroncustomjavaobject"></a>AndroidErrorOnCustomJavaObject

Propriété booléenne qui détermine si les types peuvent implémenter `Android.Runtime.IJavaObject` 
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

Si la valeur est true, ces types génèrent une erreur XA4212, sinon un avertissement XA4212 est généré.

Ajouté dans Xamarin.Android 8.1.

Cette propriété est définie par défaut sur `True`.

## <a name="androidexplicitcrunch"></a>AndroidExplicitCrunch

N’est plus pris en charge dans Xamarin. Android 11,0.

## <a name="androidextraaotoptions"></a>AndroidExtraAotOptions

Propriété de type chaîne qui permet de passer des options supplémentaires au compilateur mono au cours `Aot` de la tâche pour les projets qui ont ou ont la [`$(AndroidEnableProfiledAot)`](#androidenableprofiledaot) [`$(AotAssemblies)`](#aotassemblies) valeur `true` .
La valeur de chaîne de la propriété est ajoutée au fichier réponse lors de l’appel du compilateur croisé mono.

En général, cette propriété doit être laissée vide, mais dans certains scénarios spéciaux, elle peut fournir une flexibilité utile.

Notez que cette propriété est différente de la `$(AndroidAotAdditionalArguments)` propriété associée. Cette propriété place les arguments séparés par des virgules dans l' `--aot` option du compilateur mono. `$(AndroidExtraAotOptions)` au lieu de cela, passe des options autonomes et séparées par des espaces, comme `--verbose` ou `--debug` au compilateur.

Ajouté dans Xamarin. Android 10,2.

## <a name="androidfastdeploymenttype"></a>AndroidFastDeploymentType

`:`Liste de valeurs séparées par des virgules pour contrôler les types qui peuvent être déployés dans le [Répertoire de déploiement rapide](~/android/deploy-test/building-apps/build-process.md#Fast_Deployment) sur l’appareil cible lorsque la [`$(EmbedAssembliesIntoApk)`](#embedassembliesintoapk) propriété MSBuild est `False` . Si une ressource est déployée via le déploiement rapide, elle n’est *pas* incorporée dans le fichier `.apk` généré, ce qui peut accélérer les temps de déploiement. (Plus il s’agit d’un déploiement rapide, moins il est `.apk` nécessaire de reconstruire, et le processus d’installation peut être plus rapide.) Les valeurs valides sont les suivantes :

- `Assemblies` : déployer les assemblys de l’application.

- `Dexes` : déployer les fichiers `.dex`, les ressources Android et les composants Android. **Cette valeur peut être utilisée *uniquement* sur les appareils exécutant Android 4.4 ou ultérieur (API-19).**

La valeur par défaut est `Assemblies`.

**Expérimental**. Ajouté dans Xamarin.Android 6.1.

## <a name="androidgeneratejnimarshalmethods"></a>AndroidGenerateJniMarshalMethods

Propriété bool qui permet la génération de méthodes marshals JNI dans le cadre du processus de génération. Cela réduit considérablement l' `System.Reflection` utilisation du code d’assistance de liaison.

La valeur par défaut est False. Si les développeurs souhaitent utiliser la nouvelle fonctionnalité de méthodes de marshaling JNI, ils peuvent définir

```xml
<AndroidGenerateJniMarshalMethods>True</AndroidGenerateJniMarshalMethods>
```

dans leur `.csproj` . Vous pouvez également fournir la propriété sur la ligne de commande via

```shell
/p:AndroidGenerateJniMarshalMethods=True
```

**Expérimental**. Ajouté dans Xamarin.Android 9.2.
La valeur par défaut est False.

## <a name="androidgeneratejnimarshalmethodsadditionalarguments"></a>AndroidGenerateJniMarshalMethodsAdditionalArguments

Propriété de type chaîne qui peut être utilisée pour ajouter des paramètres supplémentaires à l' `jnimarshalmethod-gen.exe` appel.  Cette propriété est utile pour le débogage et rendre possible l’utilisation d’options telles que `-v`, `-d`, ou `--keeptemp`.

La valeur par défaut est une chaîne vide. Il peut être défini dans le `.csproj` fichier ou sur la ligne de commande. Par exemple :

```xml
<AndroidGenerateJniMarshalMethodsAdditionalArguments>-v -d --keeptemp</AndroidGenerateJniMarshalMethodsAdditionalArguments>
```

ou :

```shell
/p:AndroidGenerateJniMarshalMethodsAdditionalArguments="-v -d --keeptemp"
```

Ajouté dans Xamarin.Android 9.2.

## <a name="androidgeneratelayoutbindings"></a>AndroidGenerateLayoutBindings

Active la génération de [code-behind de disposition](https://github.com/xamarin/xamarin-android/blob/master/Documentation/guides/LayoutCodeBehind.md) si la valeur `true` est ou le désactive complètement si la valeur est `false` . La valeur par défaut est `false`.

## <a name="androidhttpclienthandlertype"></a>AndroidHttpClientHandlerType

Contrôle l’implémentation par défaut `System.Net.Http.HttpMessageHandler` qui sera utilisée par le `System.Net.Http.HttpClient` constructeur par défaut. La valeur est un nom de type qualifié d’assembly d’une `HttpMessageHandler` sous-classe, pouvant être utilisée avec [`System.Type.GetType(string)`](/dotnet/api/system.type.gettype#System_Type_GetType_System_String_) .
Les valeurs les plus couramment utilisées pour cette propriété sont :

- `Xamarin.Android.Net.AndroidClientHandler`: Utilisez les API Java Android pour effectuer des demandes réseau. Cela permet d’accéder aux URL TLS 1.2 lorsque la version Android sous-jacente prend en charge TLS 1.2. Seuls Android 5.0 et les versions ultérieures gèrent TLS 1.2 de façon fiable avec Java.

  Cela correspond à l’option **Android** des pages de propriétés Visual Studio, et à l’option **AndroidClientHandler** dans les pages de propriétés Visual Studio pour Mac.

  L’Assistant Nouveau projet sélectionne cette option pour les nouveaux projets lorsque la **Version minimale d’Android** est configurée sur **Android 5.0 (Lollipop)** ou version ultérieure dans Visual Studio, ou lorsque **Plateformes cibles** est défini sur **Tout dernier** dans Visual Studio pour Mac.

- Unset/la chaîne vide : cela équivaut à `System.Net.Http.HttpClientHandler, System.Net.Http`

  Cela correspond à l’option **Par défaut** dans les pages de propriétés de Visual Studio.

  L’Assistant Nouveau projet sélectionne cette option pour les nouveaux projets lorsque la **Version minimale d’Android** est configurée sur **Android 4.4.87** ou version antérieure dans Visual Studio, ou lorsque **Plateformes cibles** est défini sur **Développement moderne** ou **Compatibilité maximale** dans Visual Studio pour Mac.

- `System.Net.Http.HttpClientHandler, System.Net.Http`: Utilisez le managé `HttpMessageHandler` .

  Cela correspond à l’option **Managé** dans les pages de propriétés de Visual Studio.

> [!NOTE]
> Si la prise en charge de TLS 1,2 est requise sur les versions d’Android antérieures à 5,0, *ou* si la prise en charge de TLS 1,2 est requise avec `System.Net.WebClient` et les API associées, [`$(AndroidTlsProvider)`](#androidtlsprovider) doit être utilisé.

> [!NOTE]
> La prise en charge de cette propriété fonctionne en définissant la [ `XA_HTTP_CLIENT_HANDLER_TYPE` variable d’environnement](~/android/deploy-test/environment.md).
> `$XA_HTTP_CLIENT_HANDLER_TYPE`Valeur trouvée dans un fichier avec une action de génération[`@(AndroidEnvironment)`](~/android/deploy-test/building-apps/build-items.md#androidenvironment)
> est prioritaire.

Ajouté dans Xamarin.Android 6.1.

## <a name="androidkeystore"></a>AndroidKeyStore

Valeur booléenne qui indique si les informations de signature personnalisées doivent être utilisées. La valeur par défaut est `False`, qui signifie que la clé de signature de débogage par défaut est utilisée pour signer les packages.

## <a name="androidlaunchactivity"></a>AndroidLaunchActivity

Activité Android à lancer.

## <a name="androidlinkmode"></a>AndroidLinkMode

Spécifie le type de [liaison](~/android/deploy-test/linker.md) à effectuer sur les assemblys contenus dans le package Android. Utilisé seulement dans les projets d’application Android. La valeur par défaut est *SdkOnly*. Les valeurs valides sont les suivantes :

- **None** : aucune liaison n’est tentée.

- **SdkOnly** : la liaison est effectuée seulement sur les bibliothèques de classes de base, pas sur les assemblys de l’utilisateur.

- **Full** : la liaison est effectuée seulement sur les bibliothèques de classes de base et sur les assemblys de l’utilisateur.

  > [!NOTE]
  > L’utilisation de la valeur *Full* pour `AndroidLinkMode` aboutit généralement à des applications endommagées, en particulier quand la réflexion est utilisée. À éviter, sauf si vous savez *vraiment* ce que vous faites.

```xml
<AndroidLinkMode>SdkOnly</AndroidLinkMode>
```

## <a name="androidlinkskip"></a>AndroidLinkSkip

Spécifie une liste délimitée par des points-virgules ( `;` ) des noms d’assemblys, sans extensions de fichier, des assemblys qui ne doivent pas être liés. Utilisé seulement dans les projets d’application Android.

```xml
<AndroidLinkSkip>Assembly1;Assembly2</AndroidLinkSkip>
```

## <a name="androidlinktool"></a>AndroidLinkTool

Propriété de style enum avec des valeurs valides de `proguard` ou `r8` . Indique le réducteur de code utilisé pour le code Java. La valeur par défaut est une chaîne vide, ou `proguard` si `$(AndroidEnableProguard)` est `True`. Pour plus d’informations, consultez notre documentation sur [D8 et R8][d8-r8].

[d8-r8]: https://github.com/xamarin/xamarin-android/blob/master/Documentation/guides/D8andR8.md

## <a name="androidlintenabled"></a>AndroidLintEnabled

Propriété bool qui permet au développeur d’exécuter l’outil Android dans `lint` le cadre du processus d’empaquetage.

Quand `$(AndroidLintEnabled)` = true, les propriétés suivantes sont utilisées :

- [`$(AndroidLintEnabledIssues)`](#androidlintenabledissues):
- [`$(AndroidLintDisabledIssues)`](#androidlintdisabledissues):
- [`$(AndroidLintCheckIssues)`](#androidlintcheckissues):

Les actions de génération suivantes peuvent également être utilisées :

- [`@(AndroidLintConfig)`](~/android/deploy-test/building-apps/build-items.md#androidlintconfig):

Consultez l’[aide de Lint](https://developer.android.com/studio/write/lint) pour plus d’informations sur l’outil `lint` android.

## <a name="androidlintenabledissues"></a>AndroidLintEnabledIssues

Cette propriété est utilisée uniquement lorsque [`$(AndroidLintEnabled)`](#androidlintenabled) = true.

Liste séparée par des virgules des problèmes Lint à activer.

## <a name="androidlintdisabledissues"></a>AndroidLintDisabledIssues

Cette propriété est utilisée uniquement lorsque [`$(AndroidLintEnabled)`](#androidlintenabled) = true.

Liste séparée par des virgules des problèmes Lint à désactiver.

## <a name="androidlintcheckissues"></a>AndroidLintCheckIssues

Cette propriété est utilisée uniquement lorsque [`$(AndroidLintEnabled)`](#androidlintenabled) = true.

Liste séparée par des virgules des problèmes Lint à vérifier.

Remarque : Seuls ces problèmes sont vérifiés.

## <a name="androidmanagedsymbols"></a>AndroidManagedSymbols

Propriété booléenne qui contrôle si les points de séquence sont générés afin que les informations de nom de fichier et de numéro de ligne puissent être extraites des traces de la `Release` pile.

Ajouté dans Xamarin.Android 6.1.

## <a name="androidmanifest"></a>AndroidManifest

Spécifie un nom de fichier à utiliser comme modèle pour l’application [`AndroidManifest.xml`](~/android/platform/android-manifest.md) .
Lors de la génération, toutes les autres valeurs nécessaires y sont fusionnées pour produire le fichier `AndroidManifest.xml` effectif.
`$(AndroidManifest)` doit contenir le nom du package dans l’attribut `/manifest/@package`.

## <a name="androidmanifestmerger"></a>AndroidManifestMerger

Spécifie l’implémentation pour la fusion de fichiers *AndroidManifest.xml* . Il s’agit d’une propriété de style enum dans laquelle `legacy` sélectionne l’implémentation C# d’origine et `manifestmerger.jar` sélectionne l’implémentation Java de Google.

La valeur par défaut est actuellement `legacy` . Cela passera à `manifestmerger.jar` dans une version ultérieure pour aligner le comportement avec Android Studio.

La fusion de Google permet la prise en charge de `xmlns:tools="http://schemas.android.com/tools"` comme décrit dans la [documentation Android][manifest-merger].

Introduit dans Xamarin. Android 10,2

[manifest-merger]: https://developer.android.com/studio/build/manifest-merge

## <a name="androidmanifestplaceholders"></a>AndroidManifestPlaceholders

Liste de paires de remplacement de clé-valeur séparées par des points-virgules pour *AndroidManifest.xml*, où chaque paire a le format `key=value` .

Par exemple, une valeur de propriété de `assemblyName=$(AssemblyName)` définit un `${assemblyName}` espace réservé qui peut ensuite apparaître dans *AndroidManifest.xml*:

```xml
<application android:label="${assemblyName}"
```

Cela permet d’insérer des variables à partir du processus de génération dans le fichier *AndroidManifest.xml* .

## <a name="androidmultidexclasslistextraargs"></a>AndroidMultiDexClassListExtraArgs

Propriété de type chaîne qui permet aux développeurs de passer des arguments supplémentaires au lors de la `com.android.multidex.MainDexListBuilder` génération du `multidex.keep` fichier.

Un cas spécifique est si vous obtenez l’erreur suivante durant la compilation `dx`.

```text
com.android.dex.DexException: Too many classes in --main-dex-list, main dex capacity exceeded
```

Si vous obtenez cette erreur, vous pouvez ajouter le code suivant à l' `.csproj` .

```xml
<DxExtraArguments>--force-jumbo </DxExtraArguments>
<AndroidMultiDexClassListExtraArgs>--disable-annotation-resolution-workaround</AndroidMultiDexClassListExtraArgs>
```

Ce code devrait permettre au processus `dx` de se dérouler normalement.

Ajouté dans Xamarin.Android 8.3.

## <a name="androidpackageformat"></a>AndroidPackageFormat

Propriété de style enum avec des valeurs valides de `apk` ou `aab` . Cela indique si vous souhaitez empaqueter l’application Android sous la forme d’un [fichier APK][apk] ou d’un [bundle d’applications Android][bundle]. Les bundles d’applications sont un nouveau format pour les builds `Release` destinées à être envoyées à Google Play. La valeur par défaut est `apk`.

Lorsque `$(AndroidPackageFormat)` est défini sur `aab`, d’autres propriétés MSBuild sont définies. Celles-ci sont nécessaires pour les bundles d’applications Android :

- [`$(AndroidUseAapt2)`](~/android/deploy-test/building-apps/build-properties.md#androiduseaapt2) est de `True` .
- [`$(AndroidUseApkSigner)`](#androiduseapksigner) est de `False` .
- [`$(AndroidCreatePackagePerAbi)`](#androidcreatepackageperabi) est de `False` .

[apk]: https://en.wikipedia.org/wiki/Android_application_package
[bundle]: https://developer.android.com/platform/technology/app-bundle

## <a name="androidpackagenamingpolicy"></a>AndroidPackageNamingPolicy

Une propriété enum-style pour la spécification des noms de packages Java du code source Java généré.

Dans Xamarin. Android 10,2 et versions ultérieures, la seule valeur prise en charge est `LowercaseCrc64` .

Dans Xamarin. Android 10,1, une valeur de transition `LowercaseMD5` était également disponible et permettait de revenir au style de nom de package Java d’origine tel qu’il était utilisé dans Xamarin. Android 10,0 et versions antérieures. Cette option a été supprimée dans Xamarin. Android 10,2 pour améliorer la compatibilité avec les environnements de génération pour lesquels la conformité FIPS est appliquée.

Ajouté dans Xamarin. Android 10,1.

## <a name="androidr8ignorewarnings"></a>AndroidR8IgnoreWarnings

Spécifie la `-ignorewarnings` règle ProGuard pour `r8` . Cela permet `r8` à de continuer la compilation DEX même si certains avertissements sont rencontrés. La valeur par défaut est `True` , mais peut avoir la valeur `False` pour appliquer un comportement plus strict. Pour plus d’informations, consultez le [Manuel ProGuard](https://www.guardsquare.com/products/proguard/manual/usage) .

Ajouté dans Xamarin. Android 10,3.

## <a name="androidr8jarpath"></a>AndroidR8JarPath

Chemin d’accès à `r8.jar` utiliser avec le compilateur et le shrinkon R8. Correspond par défaut à un chemin dans l’installation de Xamarin.Android. Pour plus d’informations, consultez notre documentation sur [D8 et R8][d8-r8].

## <a name="androidresgenextraargs"></a>AndroidResgenExtraArgs

Spécifie des options de ligne de commande supplémentaires à passer à la commande **AAPT** lors du traitement des ressources et ressources Android.

## <a name="androidresgenfile"></a>AndroidResgenFile

Spécifie le nom du fichier de ressources à générer. Le modèle par défaut définit cette valeur sur `Resource.designer.cs`.

## <a name="androidsdkbuildtoolsversion"></a>AndroidSdkBuildToolsVersion

Le package d’outils de génération Android SDK fournit les outils **AAPT** et **zipalign** , entre autres. Plusieurs versions différentes du package d’outils de génération peuvent être installées en parallèle. Le package d’outils de génération choisi pour l’empaquetage est réalisé en recherchant et en utilisant, le cas échéant, une version « favorite » des outils de génération ; s’il n’en existe *pas*, le package d’outils de génération associé à la version la plus récente sera utilisé.

La propriété MSBuild `$(AndroidSdkBuildToolsVersion)` contient la version préférée des outils de génération. Le système de génération de Xamarin.Android fournit une valeur par défaut dans `Xamarin.Android.Common.targets`, et vous pouvez remplacer la valeur par défaut dans votre fichier projet et choisir une autre version des outils de génération, si (par exemple) la version la plus récente de aapt plante, alors qu’une version antérieure de aapt est connue pour fonctionner.

## <a name="androidsigningkeyalias"></a>AndroidSigningKeyAlias

Spécifie l’alias de la clé dans le magasin de clés. Il s’agit de la valeur **keytool -alias** utilisée lors de la création du magasin de clés.

## <a name="androidsigningkeypass"></a>AndroidSigningKeyPass

Spécifie le mot de passe de la clé dans le fichier de magasin de clés. C’est la valeur entrée quand `keytool` demande **Entrez le mot clé pour $(AndroidSigningKeyAlias)**.

Dans Xamarin. Android 10,0 et versions antérieures, cette propriété ne prend en charge que les mots de passe en texte brut.

Dans Xamarin. Android 10,1 et versions ultérieures, cette propriété prend également en charge les `env:` `file:` préfixes qui peuvent être utilisés pour spécifier une variable d’environnement ou un fichier qui contient le mot de passe. Ces options permettent d’empêcher l’affichage du mot de passe dans les journaux de génération.

Par exemple, pour utiliser une variable d’environnement nommée *AndroidSigningPassword*:

```xml
<PropertyGroup>
    <AndroidSigningKeyPass>env:AndroidSigningPassword</AndroidSigningKeyPass>
</PropertyGroup>
```

Pour utiliser un fichier situé à l’emplacement `C:\Users\user1\AndroidSigningPassword.txt` suivant :

```xml
<PropertyGroup>
    <AndroidSigningKeyPass>file:C:\Users\user1\AndroidSigningPassword.txt</AndroidSigningKeyPass>
</PropertyGroup>
```

> [!NOTE]
> Le `env:` préfixe n’est pas pris en charge lorsque [`$(AndroidPackageFormat)`](#androidpackageformat) a la valeur `aab` .

## <a name="androidsigningkeystore"></a>AndroidSigningKeyStore

Spécifie le nom de fichier du fichier de magasin de clés créé par `keytool` . Ceci correspond à la valeur fournie pour l’option **keytool -keystore**.

## <a name="androidsigningstorepass"></a>AndroidSigningStorePass

Spécifie le mot de passe pour [`$(AndroidSigningKeyStore)`](#androidsigningkeystore) .
C’est la valeur fournie à `keytool` lors de la création du fichier de magasin de clés et demandée avec **Entrez le mot de passe du magasin de clés :**.

Dans Xamarin. Android 10,0 et versions antérieures, cette propriété ne prend en charge que les mots de passe en texte brut.

Dans Xamarin. Android 10,1 et versions ultérieures, cette propriété prend également en charge les `env:` `file:` préfixes qui peuvent être utilisés pour spécifier une variable d’environnement ou un fichier qui contient le mot de passe. Ces options permettent d’empêcher l’affichage du mot de passe dans les journaux de génération.

Par exemple, pour utiliser une variable d’environnement nommée *AndroidSigningPassword*:

```xml
<PropertyGroup>
    <AndroidSigningStorePass>env:AndroidSigningPassword</AndroidSigningStorePass>
</PropertyGroup>
```

Pour utiliser un fichier situé à l’emplacement `C:\Users\user1\AndroidSigningPassword.txt` suivant :

```xml
<PropertyGroup>
    <AndroidSigningStorePass>file:C:\Users\user1\AndroidSigningPassword.txt</AndroidSigningStorePass>
</PropertyGroup>
```

> [!NOTE]
> Le `env:` préfixe n’est pas pris en charge lorsque [`$(AndroidPackageFormat)`](#androidpackageformat) a la valeur `aab` .

## <a name="androidsupportedabis"></a>AndroidSupportedAbis

Propriété de type chaîne qui contient une liste délimitée par des points-virgules ( `;` ) de Abi qui doit être incluse dans `.apk` .

Les valeurs prises en charge sont :

- `armeabi-v7a`
- `x86`
- `arm64-v8a` : nécessite Xamarin.Android 5.1 et ultérieur.
- `x86_64` : nécessite Xamarin.Android 5.1 et ultérieur.

## <a name="androidtlsprovider"></a>AndroidTlsProvider

Valeur de chaîne qui spécifie le fournisseur TLS à utiliser dans une application. Les valeurs possibles sont les suivantes :

- Unset/la chaîne vide : dans Xamarin. Android 7,3 et versions ultérieures, cela équivaut à `btls` .

  dans Xamarin.Android 7.1, équivaut à `legacy`.

  Cela correspond au paramètre **Par défaut** dans les pages de propriétés de Visual Studio.

- `btls` : utilise [BoringSSL](https://boringssl.googlesource.com/boringssl) pour la communication TLS avec [HttpWebRequest](xref:System.Net.HttpWebRequest).

  Cela permet d’utiliser TLS 1.2 sur toutes les versions d’Android.

  Cela correspond au paramètre **TLS 1.2+ natif** dans les pages de propriétés de Visual Studio.

- `legacy`: Dans Xamarin. Android 10,1 et versions antérieures, utilisez l’implémentation de l’historique SSL géré pour l’interaction réseau. Ceci *ne prend pas* en charge TLS 1.2.

  Cela correspond au paramètre **TLS 1.0 managé** dans les pages de propriétés de Visual Studio.

  Dans Xamarin. Android 10,2 et versions ultérieures, cette valeur est ignorée et le `btls` paramètre est utilisé.

- `default`: Cette valeur est peu susceptible d’être utilisée dans les projets Xamarin. Android. Dans ce cas, la valeur recommandée est une chaîne vide, qui correspond au paramètre **Par défaut** des pages de propriétés Visual Studio.

  La valeur `default` n’est pas disponible dans les pages de propriétés Visual Studio.

  Ceci équivaut à `legacy`.

Ajouté dans Xamarin.Android 7.1.

## <a name="androiduseaapt2"></a>AndroidUseAapt2

Propriété booléenne qui permet au développeur de contrôler l’utilisation de l' `aapt2` outil pour l’empaquetage.
Par défaut, cette valeur est false et Xamarin. Android utilise `aapt` .
Si le développeur souhaite utiliser les nouvelles `aapt2` fonctionnalités, ajoutez :

```xml
<AndroidUseAapt2>True</AndroidUseAapt2>
```

dans leur `.csproj` . Vous pouvez également fournir la propriété sur la ligne de commande :

```shell
/p:AndroidUseAapt2=True
```

Ajouté dans Xamarin.Android 8.3.

## <a name="androiduseapksigner"></a>AndroidUseApkSigner

Propriété bool qui permet au développeur d’utiliser l' `apksigner` outil plutôt que `jarsigner` .

Ajouté dans Xamarin.Android 8.2.

## <a name="androidusedefaultaotprofile"></a>AndroidUseDefaultAotProfile

Propriété bool qui permet au développeur de supprimer l’utilisation des profils AOA par défaut.

Pour supprimer les profils AOA par défaut, affectez à la propriété la valeur `false` .

Ajouté dans Xamarin. Android 10,1.

## <a name="androiduselegacyversioncode"></a>AndroidUseLegacyVersionCode

Propriété booléenne qui permet au développeur de rétablir le calcul versionCode à son ancien comportement antérieur à Xamarin. Android 8,2. Elle est RÉSERVÉE aux développeurs possédant des applications dans Google Play Store. Il est fortement recommandé d’utiliser la nouvelle [`$(AndroidVersionCodePattern)`](#androidversioncodepattern) propriété.

Ajouté dans Xamarin.Android 8.2.

## <a name="androidusemanageddesigntimeresourcegenerator"></a>AndroidUseManagedDesignTimeResourceGenerator

Propriété booléenne qui va basculer les builds au moment du design pour utiliser l’analyseur de ressources managées plutôt que `aapt` .

Ajouté dans Xamarin.Android 8.1.

## <a name="androidusesharedruntime"></a>AndroidUseSharedRuntime

Propriété booléenne qui détermine si les *packages de runtime partagé* sont requis pour exécuter l’application sur le périphérique cible. Le fait de s’appuyer sur les packages de runtime partagé permet de réduire la taille du package d’application, ce qui accélère le processus de création et de déploiement du package, aboutissant à un cycle de génération/déploiement/débogage plus rapide.

Cette propriété doit être définie sur `True` pour les versions Debug, et sur `False` pour les projets Release.

## <a name="androidversioncodepattern"></a>AndroidVersionCodePattern

Propriété de type chaîne qui permet au développeur de personnaliser le `versionCode` dans le manifeste.
Consultez [Création de code de version pour APK](~/android/deploy-test/building-apps/abi-specific-apks.md) pour plus d’informations sur le choix d’un `versionCode`.

OME exemples, si `abi` est `armeabi` et `versionCode` dans les manifestes `123` , `{abi}{versionCode}` produisent un versionCode `1123` lorsque `$(AndroidCreatePackagePerAbi)` s True, sinon génère une valeur de 123.
f `abi` est `x86_64` et `versionCode` dans le manifeste s `44` . Cela produira quand la valeur est `544` `$(AndroidCreatePackagePerAbi)` true, sinon produira une valeur de `44` .

Si nous incluons une chaîne avec remplissage à gauche `{abi}{versionCode:0000}`, ceci produit `50044`, car nous remplissons le `versionCode` à gauche avec `0`. Vous pouvez également utiliser le remplissage décimal tel que `{abi}{versionCode:D4}`
qui fait la même chose que l’exemple précédent.

Seules les chaînes avec le format de remplissage « 0 » et « Dx » sont prises en charge, car la valeur DOIT être un entier.

Éléments clés prédéfinis

- **abi**  &ndash; insère l’ABI ciblé pour l’application
  - 2 &ndash;`armeabi-v7a`
  - 3 &ndash;`x86`
  - 4 &ndash;`arm64-v8a`
  - 5 &ndash;`x86_64`

- **minSDK**  &ndash; insère la valeur minimale du SDK prise en charge à partir de `AndroidManifest.xml` ou `11` si aucune valeur n’est définie.

- **versionCode** &ndash; utilise le code de version provenant directement de `Properties\AndroidManifest.xml`.

Vous pouvez définir des éléments personnalisés en utilisant la propriété `$(AndroidVersionCodeProperties)` (définie ensuite).

La valeur par défaut est `{abi}{versionCode:D6}`. Si un développeur souhaite conserver l’ancien comportement, vous pouvez la remplacer en définissant la propriété `$(AndroidUseLegacyVersionCode)` sur `true`.

Ajouté dans Xamarin.Android 7.2.

## <a name="androidversioncodeproperties"></a>AndroidVersionCodeProperties

Propriété de type chaîne qui permet au développeur de définir des éléments personnalisés à utiliser avec [`$(AndroidVersionCodePattern)`](#androidversioncodepattern) .
Ils sont sous la forme d’une paire `key=value`. Tous les éléments dans `value` doivent être des valeurs entières. Par exemple : `screen=23;target=$(_AndroidApiLevel)`. Vous constatez que vous pouvez utiliser des propriétés MSBuild existantes ou personnalisées dans la chaîne.

Ajouté dans Xamarin.Android 7.2.

## <a name="aotassemblies"></a>AotAssemblies

Propriété booléenne qui détermine si les assemblys doivent être compilés à l’avance en code natif et inclus dans le `.apk` .

La prise en charge de cette propriété a été ajoutée dans Xamarin.Android 5.1.

Cette propriété est définie par défaut sur `False`.

## <a name="aprofutilextraoptions"></a>AProfUtilExtraOptions

Options supplémentaires à passer à `aprofutil` .

## <a name="beforegenerateandroidmanifest"></a>BeforeGenerateAndroidManifest

Les cibles MSBuild listées dans cette propriété seront exécutées directement avant `_GenerateJavaStubs` .

Ajouté dans Xamarin.Android 9.4.

## <a name="configuration"></a>Configuration

Spécifie la configuration de build à utiliser, par exemple « Debug » ou « Release ». La propriété Configuration est utilisée pour déterminer les valeurs par défaut pour d’autres propriétés qui déterminent le comportement de la cible. Vous pouvez créer des configurations supplémentaires dans votre IDE.

*Par défaut*, la `Debug` configuration entraîne la [`Install`](~/android/deploy-test/building-apps/build-targets.md#install)
les [`SignAndroidPackage`](~/android/deploy-test/building-apps/build-targets.md#signandroidpackage)
cible la création d’un package Android plus petit qui nécessite la présence d’autres fichiers et packages pour fonctionner.

La configuration par défaut entraîne `Release` l' [`Install`](~/android/deploy-test/building-apps/build-targets.md#install)
les [`SignAndroidPackage`](~/android/deploy-test/building-apps/build-targets.md#signandroidpackage)
cible la création d’un package Android *autonome*et pouvant être utilisé sans installer d’autres packages ou fichiers.

## <a name="debugsymbols"></a>DebugSymbols

Valeur booléenne qui détermine si le package Android est pouvant être *débogué*, en association avec la [`$(DebugType)`](#debugtype) propriété.
Un package pouvant être débogué contient des symboles de débogage, affecte à l' [ `//application/@android:debuggable` attribut](https://developer.android.com/guide/topics/manifest/application-element#debug) la valeur `true` et ajoute automatiquement la[`INTERNET`](https://developer.android.com/reference/android/Manifest.permission#INTERNET)
autorisation pour qu’un débogueur puisse s’attacher au processus. Une application est débogable si `DebugSymbols` est `True` *, et si* `DebugType` est une chaîne vide ou `Full`.

## <a name="debugtype"></a>DebugType

Spécifie le [type de symboles de débogage](/visualstudio/msbuild/csc-task) à générer dans le cadre de la génération, ce qui a également un impact sur la possibilité de déboguer l’application. Les valeurs possibles incluent :

- **Full** : des symboles complets sont générés. Si le [`DebugSymbols`](#debugsymbols)
  La propriété MSBuild est également `True` , le package d’application est alors débogable.

- **PdbOnly**: les symboles « PDB » sont générés. Le package d’application ne peut pas être débogué.

Si `DebugType` n’est pas défini ou est une chaîne vide, la propriété `DebugSymbols` contrôle si l’application est ou non débogable.

## <a name="embedassembliesintoapk"></a>EmbedAssembliesIntoApk

Propriété booléenne qui détermine si les assemblys de l’application doivent être incorporés dans le package d’application.

Cette propriété doit être définie sur `True` pour les versions Release, et sur `False` pour les projets Debug. Sa valeur *doit* être `True` dans les versions Debug si le déploiement rapide ne prend pas en charge l’appareil cible.

Lorsque cette propriété est `False` , la propriété [`$(AndroidFastDeploymentType)`](#androidfastdeploymenttype)
La propriété MSBuild contrôle également ce qui sera incorporé dans le `.apk` , ce qui peut affecter les durées de déploiement et de régénération.

## <a name="enablellvm"></a>EnableLLVM

Propriété booléenne qui détermine si LLVM doit être utilisé lors de la compilation anticipée des assemblys en code natif.

Le NDK Android doit être installé pour générer un projet pour lequel cette propriété est activée.

La prise en charge de cette propriété a été ajoutée dans Xamarin.Android 5.1.

Cette propriété est définie par défaut sur `False`.

Cette propriété est ignorée sauf si la [`$(AotAssemblies)`](#aotassemblies) propriété MSBuild est `True` .

## <a name="enableproguard"></a>EnableProguard

Propriété booléenne qui détermine si [ProGuard](https://developer.android.com/tools/help/proguard.html) est exécuté dans le cadre du processus d’empaquetage pour lier du code Java.

La prise en charge de cette propriété a été ajoutée dans Xamarin.Android 5.1.

Cette propriété est définie par défaut sur `False`.

Lorsque la `True` valeur est, les fichiers [@ (ProguardConfiguration)](~/android/deploy-test/building-apps/build-items.md#proguardconfiguration) sont utilisés pour contrôler `proguard` l’exécution.

## <a name="javamaximumheapsize"></a>JavaMaximumHeapSize

Spécifie la valeur de la valeur de paramètre **java** 
 `-Xmx` à utiliser lors de la génération du `.dex` fichier dans le cadre du processus d’empaquetage. Si non spécifié, l’option `-Xmx` fournit **java** avec la valeur `1G`. Ceci est couramment requis sur Windows par rapport à d’autres plateformes.

La spécification de cette propriété est nécessaire si la [ `_CompileDex` cible lève `java.lang.OutOfMemoryError` une exception ](https://bugzilla.xamarin.com/show_bug.cgi?id=18327).

Personnalisez la valeur en changeant :

```xml
<JavaMaximumHeapSize>1G</JavaMaximumHeapSize>
```

## <a name="javaoptions"></a>JavaOptions

Spécifie des options de ligne de commande supplémentaires à passer à **java** lors de la génération du `.dex` fichier.

## <a name="jarsignertimestampauthoritycertificatealias"></a>JarsignerTimestampAuthorityCertificateAlias

Cette propriété vous permet de spécifier un alias dans le magasin de clés pour une autorité d’horodatage.
Pour plus d’informations, consultez la documentation relative à la [prise en charge des horodateurs de signature](https://docs.oracle.com/javase/8/docs/technotes/guides/security/time-of-signing.html) Java.

```xml
<PropertyGroup>
    <JarsignerTimestampAuthorityCertificateAlias>Alias</JarsignerTimestampAuthorityCertificateAlias>
</PropertyGroup>
```

## <a name="jarsignertimestampauthorityurl"></a>JarsignerTimestampAuthorityUrl

Cette propriété vous permet de spécifier une URL vers un service d’autorité d’horodatage. Cela peut être utilisé pour s’assurer que votre `.apk` signature comprend un horodateur.
Pour plus d’informations, consultez la documentation relative à la [prise en charge des horodateurs de signature](https://docs.oracle.com/javase/8/docs/technotes/guides/security/time-of-signing.html) Java.

```xml
<PropertyGroup>
    <JarsignerTimestampAuthorityUrl>http://example.tsa.url</JarsignerTimestampAuthorityUrl>
</PropertyGroup>
```

## <a name="linkerdumpdependencies"></a>LinkerDumpDependencies

Propriété bool qui active la génération du fichier de dépendances de l’éditeur de liens. Ce fichier peut être utilisé en tant qu’entrée pour l’outil [illinkanalyzer](https://github.com/mono/linker/blob/master/src/analyzer/README.md).

La valeur par défaut est False.

## <a name="mandroidi18n"></a>MandroidI18n

Spécifie la prise en charge de l’internationalisation incluse avec l’application, comme les tables de classement et de tri. La valeur est une liste (délimitée par des virgules ou des points-virgules) d’une ou plusieurs des valeurs suivantes, qui ne respectent pas la casse :

- **None** : n’inclure aucun encodage supplémentaire.

- **All** : inclure tous les encodages disponibles.

- **CJK** : incluent les encodages du chinois, du japonais et du coréen, comme *Japonais (EUC)* \[enc-jp, CP51932\], *Japonais (Shift-JIS)* \[ ISO-2022-jp, shift\_jis, CP932\], *Japonais (JIS)* \[CP50220\], *Chinois simplifié (GB2312)* \[gb2312, CP936\], *Coréen (UHC)* \[ks\_c\_5601-1987, CP949\], *Coréen (EUC)* \[euc-kr, CP51949\], *Chinois traditionnel (Big5)* \[big5, CP950\] et *Chinois simplifié (GB18030)* \[ GB18030, CP54936\].

- **MidEast** : inclure les encodages du Moyen-Orient, comme *Turc (Windows)* \[iso-8859-9, CP1254\], *Hébreu (Windows)* \[windows-1255, CP1255\], *Arabe (Windows)* \[windows-1256, CP1256\], *Arabe (ISO)* \[iso-8859-6, CP28596\], *Hébreu (ISO)* \[iso-8859-8, CP28598\], *Latin 5 (ISO)* \[iso-8859-9, CP28599\] et *Hébreu (Iso Alternative)* \[iso-8859-8, CP38598\].

- **Other** : inclure d’autres encodages, comme *Cyrillique (Windows)* \[CP1251\], *Balte (Windows)* \[iso-8859-4, CP1257\], *Vietnamien (Windows)* \[CP1258\], *Cyrillique (KOI8-R)* \[koi8-r, CP1251\], *Ukrainien (KOI8-U)* \[koi8-u, CP1251\], *Balte (ISO)* \[iso-8859-4, CP1257\], *Cyrillique (ISO)* \[iso-8859-5, CP1251\], *ISCII Devanagari* \[x-iscii-de, CP57002\], *Bengali ISCII* \[x-iscii-be, CP57003\], *Tamoul ISCII* \[x-iscii-ta, CP57004\], *Télougou ISCII* \[x-iscii-te, CP57005\], *Assamais ISCII* \[x-iscii-as, CP57006\], *Oriya ISCII* \[x-iscii-or, CP57007\], *Kannada ISCII* \[x-iscii-ka, CP57008\], *Malayalam ISCII* \[x-iscii-ma, CP57009\], *Gujarati ISCII* \[x-iscii-gu, CP57010\], *Punjabi ISCII* \[x-iscii-pa, CP57011\] et *Thaï (Windows)* \[CP874\].

- **Rare** : inclure des encodages rares, comme *IBM EBCDIC (Turc)* \[CP1026\], *IBM EBCDIC (Systèmes ouverts Latin 1)* \[CP1047\], *IBM EBCDIC (É.U. - Canada avec Euro)* \[CP1140\], *IBM EBCDIC (Allemagne avec Euro)* \[CP1141\], *IBM EBCDIC (Danemark/Norvège avec Euro)* \[CP1142\], *IBM EBCDIC (Finlande/Suède avec Euro)* \[CP1143\], *IBM EBCDIC (Italie avec Euro)* \[CP1144\], *IBM EBCDIC (Amérique latine/Espagne avec Euro)* \[CP1145\], *IBM EBCDIC (Royaume-Uni avec Euro)* \[CP1146\], *IBM EBCDIC (France avec Euro)* \[CP1147\], *IBM EBCDIC (International avec Euro)* \[CP1148\], *IBM EBCDIC (Islandais avec Euro)* \[CP1149\], *IBM EBCDIC (Allemagne)* \[CP20273\], *IBM EBCDIC (Danemark/Norvège)* \[CP20277\], *IBM EBCDIC (Finlande/Suède)* \[CP20278\], *IBM EBCDIC (Italie)* \[CP20280\], *IBM EBCDIC (Amérique latine/Espagne)* \[CP20284\], *IBM EBCDIC (Royaume-Uni)* \[CP20285\], *IBM EBCDIC (Japonais Katakana étendu)* \[CP20290\], *IBM EBCDIC (France)* \[CP20297\], *IBM EBCDIC (Arabe)* \[CP20420\], *IBM EBCDIC (Hébreu)* \[CP20424\], *IBM EBCDIC (Islandais)* \[CP20871\], *IBM EBCDIC (Cyrillique - Serbe, Bulgare)* \[CP21025\], *IBM EBCDIC (É.U. - Canada)* \[CP37\], *IBM EBCDIC (International)* \[CP500\], *Arabe (ASMO 708)* \[CP708\], *Europe centrale (DOS)* \[CP852\]*, Cyrillique (DOS)* \[CP855\], *Turc (DOS)* \[CP857\], *Europe de l’Ouest (DOS avec Euro)* \[CP858\], *Hébreu (DOS)* \[CP862\], *Arabe (DOS)* \[CP864\], *Russe (DOS)* \[CP866\], *Grec (DOS)* \[CP869\], *IBM EBCDIC (Latin 2)* \[CP870\] et *IBM EBCDIC (Grec)* \[CP875\].

- **West** : inclure les encodages occidentaux, comme *Europe de l’Ouest (Mac)* \[macintosh, CP10000\], *Islandais (Mac)* \[x-mac-icelandic, CP10079\], *Europe centrale (Windows)* \[iso-8859-2, CP1250\], *Europe centrale (Windows)* \[iso-8859-1, CP1252\], *Grec (Windows)* \[iso-8859-7, CP1253\], *Europe centrale (ISO)* \[iso-8859-2, CP28592\], *Latin 3 (ISO)* \[iso-8859-3, CP28593\], *Grec (ISO)* \[iso-8859-7, CP28597\], *Latin 9 (ISO)* \[iso-8859-15, CP28605\], *OEM États-Unis* \[CP437\], *Europe de l’Ouest (DOS)* \[CP850\], *Portugais (DOS)* \[CP860\], *Islandais (DOS)* \[CP861\], *Français (Canada) (DOS)* \[CP863\] et *Nordique (DOS)* \[CP865\].

```xml
<MandroidI18n>West</MandroidI18n>
```

## <a name="monoandroidresourceprefix"></a>MonoAndroidResourcePrefix

Spécifie un *préfixe de chemin d’accès* qui est supprimé du début des noms de fichiers avec une action de génération `AndroidResource` . Ceci permet de changer l’emplacement où se trouvent les ressources.

La valeur par défaut est `Resources`. Changez ceci en `res` pour la structure de projet Java.

## <a name="monosymbolarchive"></a>MonoSymbolArchive

Propriété booléenne qui contrôle si les `.mSYM` artefacts sont créés pour une utilisation ultérieure avec `mono-symbolicate` , pour extraire les &ldquo; &rdquo; informations de nom de fichier et de numéro de ligne réelles des traces de la pile de mise en sortie.

C’est le cas par défaut pour les &ldquo; applications de version pour &rdquo; lesquelles les symboles de débogage sont activés : [`$(EmbedAssembliesIntoApk)`](#embedassembliesintoapk) a la valeur true, [`$(DebugSymbols)`](~/android/deploy-test/building-apps/build-properties.md#debugsymbols)
a la valeur true et [`$(Optimize)`](/visualstudio/msbuild/common-msbuild-project-properties)
a la valeur true.

Ajouté dans Xamarin.Android 7.1.