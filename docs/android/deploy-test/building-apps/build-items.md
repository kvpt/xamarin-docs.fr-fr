---
title: Éléments de génération
description: Ce document répertorie tous les groupes d’éléments pris en charge dans le processus de génération Xamarin. Android.
ms.prod: xamarin
ms.assetid: 5EBEE1A5-3879-45DD-B1DE-5CD4327C2656
ms.technology: xamarin-android
author: jonpryor
ms.author: jopryo
ms.date: 09/23/2020
ms.openlocfilehash: 8a23e973687ac9f775042685122d558788fc7be7
ms.sourcegitcommit: 145bd7550d19088c84949ecf5b1cc39002183234
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2020
ms.locfileid: "92897440"
---
# <a name="build-items"></a>Éléments de génération

Les éléments de build contrôlent le mode de génération d’une application ou d’un projet de bibliothèque Xamarin. Android.

## <a name="androidasset"></a>AndroidAsset

Prend en charge les [ressources Android](https://developer.android.com/guide/topics/resources/providing-resources#OriginalFiles), les fichiers qui seraient inclus dans le `assets` dossier d’un projet Java Android.

## <a name="androidaarlibrary"></a>AndroidAarLibrary

L’action de génération de `AndroidAarLibrary` doit être utilisée pour référencer directement des `.aar` fichiers. Elle est le plus souvent utilisée par les composants Xamarin, Notamment, pour inclure des références à des `.aar` fichiers qui sont requis pour faire fonctionner Google Play et d’autres services.

Les fichiers avec cette action de génération sont traités de la même façon que les ressources incorporées dans les projets de bibliothèque. Le `.aar` sera extrait dans le répertoire intermédiaire. Ensuite, les ressources, les ressources et `.jar` les fichiers sont inclus dans les groupes d’éléments appropriés.

## <a name="androidaotprofile"></a>AndroidAotProfile

Utilisé pour fournir un profil AOA, à utiliser avec l’AOA guidé par profil.

## <a name="androidboundlayout"></a>AndroidBoundLayout

Indique que du code-behind doit être généré pour le fichier de disposition si la propriété `AndroidGenerateLayoutBindings` est définie sur `false`. Concernant tous les autres aspects, cette action est identique à l’action `AndroidResource` décrite ci-dessus. Cette action peut être utilisée **uniquement** avec les fichiers de disposition :

```xml
<AndroidBoundLayout Include="Resources\layout\Main.axml" />
```

## <a name="androidenvironment"></a>AndroidEnvironment

Les fichiers avec une action de génération `AndroidEnvironment` sont utilisés pour [initialiser des variables d’environnement et des propriétés système lors du démarrage du processus](~/android/deploy-test/environment.md).
L’action de génération `AndroidEnvironment` peut être appliquée à plusieurs fichiers : dans ce cas, ils sont évalués sans suivre un ordre particulier (ne spécifiez donc pas la même variable d’environnement ou la même propriété système dans plusieurs fichiers).

## <a name="androidfragmenttype"></a>AndroidFragmentType

Spécifie le type complet par défaut à utiliser pour tous les éléments de disposition `<fragment>` pendant la génération du code des liaisons de disposition. Par défaut, la propriété est définie sur le type `Android.App.Fragment` Android standard.

## <a name="androidjavalibrary"></a>AndroidJavaLibrary

Les fichiers avec une action de génération `AndroidJavaLibrary` sont des archives Java (des fichiers `.jar`) qui sont inclus dans le package Android final.

## <a name="androidjavasource"></a>AndroidJavaSource

Les fichiers avec une action de génération `AndroidJavaSource` contiennent du code source Java qui est inclus dans le package Android final.

## <a name="androidlintconfig"></a>AndroidLintConfig

L’action de génération « AndroidLintConfig » doit être utilisée conjointement avec le [`$(AndroidLintEnabled)`](~/android/deploy-test/building-apps/build-properties.md#androidlintenabled)
. Les fichiers avec cette action de génération sont fusionnés et passés à l’outil `lint` android. Ces fichiers doivent être des fichiers XML qui contiennent des informations sur les tests à activer et désactiver.

Consultez la [documentation lint](https://developer.android.com/studio/write/lint) pour plus d’informations.

## <a name="androidnativelibrary"></a>AndroidNativeLibrary

Les [bibliothèques natives](~/android/platform/native-libraries.md) sont ajoutées à la build en définissant leur action de génération sur `AndroidNativeLibrary`.

Notez que comme Android prend en charge plusieurs ABI (Application Binary Interface), le système de génération doit savoir pour quelle ABI la bibliothèque native est générée. Vous pouvez faire cela de deux façons :

1. « Détection » du chemin.
2. Avec l’attribut d’élément `Abi`.

Avec la détection de chemin, le nom du répertoire parent de la bibliothèque native est utilisé pour spécifier l’ABI ciblée par la bibliothèque. Ainsi, si vous ajoutez `lib/armeabi-v7a/libfoo.so` à la build, l’ABI sera « détectée » en tant que `armeabi-v7a`.

### <a name="item-attribute-name"></a>Nom d’attribut d’élément

**Abi** &ndash; spécifie l’ABI de la bibliothèque native.

```xml
<ItemGroup>
  <AndroidNativeLibrary Include="path/to/libfoo.so">
    <Abi>armeabi-v7a</Abi>
  </AndroidNativeLibrary>
</ItemGroup>
```

## <a name="androidresource"></a>AndroidResource

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
  <MonoAndroidResourcePrefix>Resources;Resources-Debug</MonoAndroidResourcePrefix>
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

## <a name="androidresourceanalysisconfig"></a>AndroidResourceAnalysisConfig

L’action de génération `AndroidResourceAnalysisConfig` marque un fichier comme un fichier de configuration de niveau de gravité pour l’outil de diagnostic Xamarin Android designer Layout. Actuellement utilisé uniquement dans l’éditeur de disposition et non pour les messages de génération.

Pour plus d’informations, consultez la documentation sur l' [analyse des ressources Android](../../user-interface/android-designer/diagnostics.md) .

Ajouté dans Xamarin. Android 10,2.

## <a name="content"></a>Contenu

L’action de génération `Content` normale n’est pas prise en charge (comme nous n’avons pas trouvé comment la prendre en charge sans une étape de première exécution éventuellement coûteuse en ressources).

À compter de Xamarin.Android 5.1, une tentative d’utilisation de l’action de génération `@(Content)` produit un avertissement `XA0101`.

## <a name="linkdescription"></a>LinkDescription

Des fichiers avec une action de génération *LinkDescription* sont utilisés pour [contrôler le comportement de l’éditeur de liens](~/cross-platform/deploy-test/linker.md).

## <a name="proguardconfiguration"></a>ProguardConfiguration

Les fichiers avec une action de génération *ProguardConfiguration* contiennent des options qui permettent de contrôler le comportement de `proguard`. Pour plus d’informations sur cette action de génération, consultez [ProGuard](~/android/deploy-test/release-prep/proguard.md).

Ces fichiers sont ignorés, sauf si [`$(EnableProguard)`](~/android/deploy-test/building-apps/build-properties.md#enableproguard)
La propriété MSBuild est `True` .
