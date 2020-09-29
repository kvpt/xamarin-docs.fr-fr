---
title: Processus de génération
description: Ce document fournit une vue d’ensemble du processus de génération Xamarin. Android.
ms.prod: xamarin
ms.assetid: 3BE5EE1E-3FF6-4E95-7C9F-7B443EE3E94C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 09/11/2020
ms.openlocfilehash: d89f686be99dc8ae8d1aada12dcbe94d857424d7
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91454960"
---
# <a name="build-process"></a>Processus de génération

Le processus de génération Xamarin. Android est responsable du collage de tout ensemble : [génération `Resource.designer.cs` ](~/android/internals/api-design.md), prise en charge de [`@(AndroidAsset)`](~/android/deploy-test/building-apps/build-items.md#androidasset) , de [`@(AndroidResource)`](~/android/deploy-test/building-apps/build-items.md#androidresource) et d’autres [actions de génération](~/android/deploy-test/building-apps/build-items.md), génération de [wrappers pouvant être appelés par Android](~/android/platform/java-integration/android-callable-wrappers.md)et génération d’un `.apk` pour l’exécution sur des appareils Android.

## <a name="application-packages"></a>Packages d’application

En gros, il existe deux types de packages d’application Android (les fichiers `.apk`) que le système de génération Xamarin.Android peut produire :

- Des versions **Release**, qui sont entièrement autonomes et ne nécessitent pas de packages supplémentaires pour s’exécuter. Ce sont ces packages qui sont fournis à un App Store.

- Des versions **Debug**, qui ne le sont pas.

Ce n’est pas par hasard qu’elles correspondent à la `Configuration` de MSBuild qui produit le package.

## <a name="shared-runtime"></a>Runtime partagé

Le *runtime partagé* est une paire de packages Android supplémentaires qui fournissent la bibliothèque de classes de base (`mscorlib.dll`, etc.) et la bibliothèque de liaison Android (`Mono.Android.dll`, etc.). Les versions Debug s’appuient sur le runtime partagé au lieu d’inclure la bibliothèque de classes de base et les assemblys de liaison dans le package d’application Android, ce qui explique la taille plus petite du package Debug.

Le runtime partagé peut être désactivé dans les versions Debug en définissant l’option [`$(AndroidUseSharedRuntime)`](~/android/deploy-test/building-apps/build-properties.md#androidusesharedruntime)
à la propriété `False` .

<a name="Fast_Deployment"></a>

## <a name="fast-deployment"></a>Déploiement rapide

Le *déploiement rapide* fonctionne conjointement avec le runtime partagé pour réduire encore plus la taille du package d’application Android. Pour cela, les assemblys de l’application ne sont pas intégrés dans le package. Ils sont plutôt copiés sur la cible via `adb push`. Ce processus accélère le cycle de génération/déploiement/débogage, car si *seuls* des assemblys sont modifiés, le package n’est pas réinstallé. Au lieu de cela, seuls les assemblys mis à jour sont resynchronisées sur l’appareil cible.

Le déploiement rapide est connu pour échouer sur les appareils qui bloquent la synchronisation de `adb` pour le répertoire `/data/data/@PACKAGE_NAME@/files/.__override__`.

Le déploiement rapide est activé par défaut et peut être désactivé dans les versions Debug en définissant la propriété `$(EmbedAssembliesIntoApk)` sur `True`.

## <a name="msbuild-projects"></a>Projets MSBuild

Le processus de génération de Xamarin.Android est basé sur MSBuild, qui est également le format de fichier projet utilisé par Visual Studio pour Mac et Visual Studio.
En règle générale, les utilisateurs n’ont pas besoin de modifier les fichiers MSBuild manuellement, car l’IDE crée des projets entièrement fonctionnels et les met à jour avec toutes les modifications apportées, puis appelle automatiquement les cibles de génération en fonction des besoins.

Les utilisateurs avancés peuvent néanmoins souhaiter effectuer des opérations non prises en charge par l’interface graphique utilisateur de l’IDE : le processus de génération est donc personnalisable en modifiant le fichier projet directement.
Cette page décrit seulement les fonctionnalités et les personnalisations spécifiques à Xamarin.Android : bien d’autres choses sont possibles avec les éléments, les propriétés et les cibles normales de MSBuild.

<a name="Build_Targets"></a>

## <a name="binding-projects"></a>Liaison de projets

Les propriétés MSBuild suivantes sont utilisées avec les [projets de liaison](~/android/platform/binding-java-library/index.md) :

- [`$(AndroidClassParser)`](~/android/deploy-test/building-apps/build-properties.md#androidclassparser)
- [`$(AndroidCodegenTarget)`](~/android/deploy-test/building-apps/build-properties.md#androidcodegentarget)

## <a name="resourcedesignercs-generation"></a>`Resource.designer.cs` Toute

Les propriétés MSBuild suivantes permettent de contrôler la génération du `Resource.designer.cs` fichier :

- [`$(AndroidAapt2CompileExtraArgs)`](~/android/deploy-test/building-apps/build-properties.md#androidaapt2compileextraargs)
- [`$(AndroidAapt2LinkExtraArgs)`](~/android/deploy-test/building-apps/build-properties.md#androidaapt2linkextraargs)
- [`$(AndroidExplicitCrunch)`](~/android/deploy-test/building-apps/build-properties.md#androidexplicitcrunch)
- [`$(AndroidR8IgnoreWarnings)`](~/android/deploy-test/building-apps/build-properties.md#androidr8ignorewarnings)
- [`$(AndroidResgenExtraArgs)`](~/android/deploy-test/building-apps/build-properties.md#androidresgenextraargs)
- [`$(AndroidResgenFile)`](~/android/deploy-test/building-apps/build-properties.md#androidresgenfile)
- [`$(AndroidUseAapt2)`](~/android/deploy-test/building-apps/build-properties.md#androiduseaapt2)
- [`$(MonoAndroidResourcePrefix)`](~/android/deploy-test/building-apps/build-properties.md#monoandroidresourceprefix)

## <a name="signing-properties"></a>Propriétés de signature

Les propriétés de signature contrôlent comment le package d’application est signé pour qu’il puisse être installé sur un appareil Android. Pour permettre une itération plus rapide des générations, les tâches Xamarin.Android ne signent pas les packages pendant le processus de génération, car l’opération de signature est assez lente. Au lieu de cela, les packages sont signés (si nécessaire) avant l’installation ou pendant l’exportation, par l’IDE ou par la cible de génération *Install*. Le fait d’appeler la cible *SignAndroidPackage* produit un package avec le suffixe `-Signed.apk` dans le répertoire de sortie.

Par défaut, la cible de signature génère si nécessaire une nouvelle clé de signature de débogage. Si vous souhaitez utiliser une clé spécifique, par exemple sur un serveur de builds, les propriétés MSBuild suivantes sont utilisées :

- [`$(AndroidDebugKeyAlgorithm)`](~/android/deploy-test/building-apps/build-properties.md#androiddebugkeyalgorithm)
- [`$(AndroidDebugKeyValidity)`](~/android/deploy-test/building-apps/build-properties.md#androiddebugkeyvalidity)
- [`$(AndroidDebugStoreType)`](~/android/deploy-test/building-apps/build-properties.md#androiddebugstoretype)
- [`$(AndroidKeyStore)`](~/android/deploy-test/building-apps/build-properties.md#androidkeystore)
- [`$(AndroidSigningKeyAlias)`](~/android/deploy-test/building-apps/build-properties.md#androidsigningkeyalias)
- [`$(AndroidSigningKeyPass)`](~/android/deploy-test/building-apps/build-properties.md#androidsigningkeypass)
- [`$(AndroidSigningKeyStore)`](~/android/deploy-test/building-apps/build-properties.md#androidsigningkeystore)
- [`$(AndroidSigningStorePass)`](~/android/deploy-test/building-apps/build-properties.md#androidsigningstorepass)
- [`$(JarsignerTimestampAuthorityCertificateAlias)`](~/android/deploy-test/building-apps/build-properties.md#jarsignertimestampauthoritycertificatealias)
- [`$(JarsignerTimestampAuthorityUrl)`](~/android/deploy-test/building-apps/build-properties.md#jarsignertimestampauthorityurl)

### <a name="keytool-option-mapping"></a>`keytool` Mappage des options

Examinez l' `keytool` appel suivant :

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

Les points d’extension sont les suivants :

- [' $ (AfterGenerateAndroidManifest)](~/android/deploy-test/building-apps/build-properties.md#aftergenerateandroidmanifest)
- [' $ (BeforeGenerateAndroidManifest)](~/android/deploy-test/building-apps/build-properties.md#beforegenerateandroidmanifest)

Un mot de prudence pour étendre le processus de génération : s’il n’est pas écrit correctement, les extensions de build peuvent affecter les performances de votre Build, en particulier si elles s’exécutent à chaque Build. Il est vivement recommandé de lire la [documentation](/visualstudio/msbuild/msbuild) MSBuild avant d’implémenter ces extensions.

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