---
title: Localisation Android
description: Ce document présente les fonctionnalités de localisation du Android SDK et explique comment y accéder avec Xamarin.
ms.prod: xamarin
ms.assetid: D1277939-A1E8-468E-B136-820D816AF853
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: 08d12a58b4a0d8a8f757df965bd9dfb73f1639b1
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70755377"
---
# <a name="android-localization"></a>Localisation Android

_Ce document présente les fonctionnalités de localisation du Android SDK et explique comment y accéder avec Xamarin._

## <a name="android-platform-features"></a>Fonctionnalités de la plateforme Android

Cette section décrit les principales fonctionnalités de localisation d’Android. Passez à la [section suivante](#basics) pour afficher du code spécifique et des exemples.

### <a name="locale"></a>Paramètres régionaux

Les utilisateurs choisissent leur langue dans **paramètres > langue & entrée**. Cette sélection contrôle à la fois la langue affichée et les paramètres régionaux utilisés (par exemple, pour la mise en forme des dates et des nombres).

Les paramètres régionaux actuels peuvent être interrogés via le du `Resources`contexte actuel :

```csharp
var lang = Resources.Configuration.Locale; // eg. "es_ES"
```

Cette valeur est un identificateur de paramètres régionaux qui contient un code de langue et un code de paramètres régionaux, séparés par un trait de soulignement. Pour référence, voici une [liste de paramètres régionaux Java](https://www.oracle.com/technetwork/java/javase/locales-137662.html) et de [paramètres régionaux pris en charge par Android via StackOverflow](https://stackoverflow.com/questions/7973023/what-is-the-list-of-supported-languages-locales-on-android).

Voici quelques exemples usuels :

- `en_US`pour l’anglais (États-Unis)
- `es_ES`pour l’espagnol (Espagne)
- `ja_JP`pour le japonais (Japon)
- `zh_CN`pour le chinois (Chine)
- `zh_TW`pour le chinois (Taïwan)
- `pt_PT`pour le portugais (Portugal)
- `pt_BR`pour le portugais (Brésil)

### <a name="locale_changed"></a>LOCALE_CHANGED

Android génère `android.intent.action.LOCALE_CHANGED` lorsque l’utilisateur modifie la sélection de la langue.

Les activités peuvent choisir de gérer cela en définissant l' `android:configChanges` attribut sur l’activité, comme suit :

```csharp
[Activity (Label = "@string/app_name", MainLauncher = true, Icon="@drawable/launcher",
    ConfigurationChanges = ConfigChanges.Locale | ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
```

<a name="basics" />

## <a name="internationalization-basics-in-android"></a>Notions de base de l’internationalisation dans Android

La stratégie de localisation d’Android a les éléments clés suivants :

- Dossiers de ressources pour contenir des chaînes, des images et d’autres ressources localisées.

- `GetText`méthode, qui est utilisée pour récupérer des chaînes localisées dans le code

- `@string/id`dans les fichiers AXML, pour placer automatiquement les chaînes localisées dans les mises en page.

### <a name="resource-folders"></a>Dossiers de ressources

Les applications Android gèrent la plupart des contenus dans les dossiers de ressources, par exemple :

- **disposition** : contient les fichiers de disposition AXML.
- **dessinable** -contient des images et d’autres ressources dessinables.
- **valeurs** : contient des chaînes.
- **RAW** -contient des fichiers de données.

La plupart des développeurs sont déjà familiarisés avec l’utilisation de suffixes **PPP** sur le répertoire de **dessin** pour fournir plusieurs versions d’une image, ce qui permet à Android de choisir la version appropriée pour chaque appareil. Le même mécanisme est utilisé pour fournir plusieurs traductions de langue en suffixant des répertoires de ressources avec des identificateurs de langue et de culture.

![Capture d’écran de dossiers de ressources/de dessins et de ressources/valeurs pour plusieurs identificateurs culturels](localization-images/resources.png)

> [!NOTE]
> Lorsque vous spécifiez un langage de niveau `es` supérieur, comme seulement deux caractères sont requis ; Toutefois, lorsque vous spécifiez des paramètres régionaux complets, le format du nom de répertoire nécessite un tiret et un **r** minuscule pour séparer les deux parties, par exemple **PT-rBR** ou  **zh-rCN**. Comparez cela à la valeur retournée dans le code, qui a un trait de soulignement (par exemple, `pt_BR`). Les deux sont différents de la valeur utilisée par `CultureInfo` la classe .net, qui a un tiret uniquement (par exemple, `pt-BR`). Gardez ces différences à l’esprit lorsque vous travaillez sur des plateformes Xamarin.

#### <a name="stringsxml-file-format"></a>Format de fichier Strings. Xml

Un répertoire de **valeurs** localisé (par exemple, **values-es** ou **values-PT-rBR**) doit contenir un fichier nommé **Strings. xml** qui contient le texte traduit pour ces paramètres régionaux.

Chaque chaîne convertible est un élément XML avec l’ID de ressource spécifié comme `name` attribut et la chaîne traduite comme valeur :

```xml
<string name="app_name">TaskyL10n</string>
```

Vous devez utiliser une séquence d’échappement en fonction des règles XML `name` normales, et doit être un ID de ressource Android valide (sans espaces ni tirets). Voici un exemple de fichier de chaînes par défaut (anglais) pour l’exemple :

**values/Strings.xml**

```xml
<resources>
    <string name="app_name">TaskyL10n</string>
    <string name="taskadd">Add Task</string>
    <string name="taskname">Name</string>
    <string name="tasknotes">Notes</string>
    <string name="taskdone">Done</string>
    <string name="taskcancel">Cancel</string>
</resources>
```

Les valeurs de répertoire espagnol **-es** contiennent un fichier portant le même nom (**Strings. xml**) qui contient les traductions :

**values-es/Strings.xml**

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="app_name">TaskyLeon</string>
    <string name="taskadd">agregar tarea</string>
    <string name="taskname">Nombre</string>
    <string name="tasknotes">Notas</string>
    <string name="taskdone">Completo</string>
    <string name="taskcancel">Cancelar</string>
</resources>
```

![Capture d’écran de plusieurs dossiers de valeurs, chacun contenant un fichier Strings. Xml](localization-images/values.png)

Avec la configuration des fichiers de chaînes, les valeurs traduites peuvent être référencées dans des dispositions et du code.

### <a name="axml-layout-files"></a>Fichiers de disposition AXML

Pour référencer des chaînes localisées dans des fichiers de `@string/id` disposition, utilisez la syntaxe. Cet extrait XML de l’exemple montre `text` des propriétés qui sont définies avec des ID de ressource localisés (d’autres attributs ont été omis) :

```xml
<TextView
    android:id="@+id/NameLabel"
    android:text="@string/taskname"
    ... />
<CheckBox
    android:id="@+id/chkDone"
    android:text="@string/taskdone"
    ... />
```

### <a name="gettext-method"></a>GetText, méthode

Pour récupérer des chaînes traduites dans le code `GetText` , utilisez la méthode et transmettez l’ID de ressource :

```csharp
var cancelText = Resources.GetText (Resource.String.taskcancel);
```

#### <a name="quantity-strings"></a>Chaînes de quantité

Les ressources de chaîne Android vous permettent également de créer des *chaînes de quantité* qui permettent aux traducteurs de fournir des traductions différentes pour différentes quantités, par exemple :

- « Il y a 1 tâche restante ».
- « Il y a 2 tâches toujours à effectuer. »

(plutôt qu’un générique « il y a n tâches à gauche »).

Dans le **fichier Strings. xml**

```xml
<plurals name="numberOfTasks">
   <!--
      As a developer, you should always supply "one" and "other"
      strings. Your translators will know which strings are actually
      needed for their language.
    -->
   <item quantity="one">There is %d task left.</item>
   <item quantity="other">There are %d tasks still to do.</item>
 </plurals>
```

Pour afficher la chaîne complète, utilisez `GetQuantityString` la méthode, en passant l’ID de ressource et la valeur à afficher (qui est passé deux fois). Le deuxième paramètre est utilisé par Android *pour déterminer* `quantity` la chaîne à utiliser, le troisième paramètre est la valeur en fait remplacée dans la chaîne (les deux sont obligatoires).

```csharp
var translated = Resources.GetQuantityString (
                    Resource.Plurals.numberOfTasks, taskcount, taskcount);`
```

Les `quantity` commutateurs valides sont les suivants :

- zéro
- one
- directionnelle
- prochains
- many
- other

Elles sont décrites plus en détail dans les [documents Android](https://developer.android.com/guide/topics/resources/string-resource.html#Plurals). Si une langue donnée ne requiert pas de gestion « spéciale », `quantity` ces chaînes seront ignorées (par exemple, l’anglais `one` utilise `other`uniquement et ; `zero` la spécification d’une chaîne n’aura aucun effet, elle ne sera pas utilisée).

### <a name="images"></a>Images

Les images localisées suivent les mêmes règles que les fichiers de chaînes : toutes les images référencées dans l’application doivent être placées dans des répertoires **dessinables** afin qu’il y ait un secours.

Les images spécifiques aux paramètres régionaux doivent ensuite être placées dans des dossiers pouvant faire l’objet d’un dessin (par exemple, les spécificateurs de type **«** ou- **ja** » peuvent également être ajoutés).

Dans cette capture d’écran, quatre images sont enregistrées dans le répertoire de **dessin** , mais une seule, **Flag. png**, contient des copies localisées dans d’autres répertoires.

![Capture d’écran de plusieurs dossiers dessinables, chacun contenant un ou plusieurs fichiers. png localisés](localization-images/drawable.png)

#### <a name="other-resource-types"></a>Autres types de ressources

Vous pouvez également fournir d’autres types de ressources spécifiques à une langue, notamment les dispositions, les animations et les fichiers bruts. Cela signifie que vous pouvez fournir une disposition d’écran spécifique pour une ou plusieurs de vos langues cibles, par exemple, vous pouvez créer une disposition spécifiquement pour l’allemand qui autorise des étiquettes de texte très longues.

Android 4,2 a introduit la prise en charge des [langues de droite à gauche (RTL)](http://android-developers.blogspot.fr/2013/03/native-rtl-support-in-android-42.html) si `android:supportsRtl="true"`vous définissez le paramètre d’application. Le qualificateur `"ldrtl"` de ressource peut être inclus dans un nom de répertoire pour contenir des dispositions personnalisées conçues pour un affichage de droite à gauche.

Pour plus d’informations sur l’affectation de noms aux répertoires de ressources et de secours, reportez-vous aux documents Android pour [obtenir d’autres ressources](https://developer.android.com/guide/topics/resources/providing-resources.html#AlternativeResources).

### <a name="app-name"></a>Nom de l’application

Le nom de l’application est facile à localiser à l' `@string/id` aide d’un `MainLauncher` dans pour l’activité :

```csharp
[Activity (Label = "@string/app_name", MainLauncher = true, Icon="@drawable/launcher",
    ConfigurationChanges =  ConfigChanges.Orientation | ConfigChanges.Locale)]
```

### <a name="right-to-left-rtl-languages"></a>Langues de droite à gauche (RTL)

Android 4,2 et versions ultérieures fournissent une prise en charge complète des dispositions RTL, décrites en détail dans le blog de support de la version [RTL de Native](http://android-developers.blogspot.dk/2013/03/native-rtl-support-in-android-42.html).

Quand vous utilisez Android 4,2 (niveau d’API 17) et plus récent, les valeurs d' `start` alignement `end` peuvent être `left` spécifiées avec et `android:paddingStart`au lieu de et `right` (par exemple). Il existe également de nouvelles API `LayoutDirection`telles `TextDirection`que, `TextAlignment` et pour aider à créer des écrans qui s’adaptent aux lecteurs RTL.

La capture d’écran suivante montre l' [exemple **Tasky** localisé](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n) en arabe :

[![Capture d’écran de l’application Tasky en arabe](localization-images/rtl-ar-sml.png)](localization-images/rtl-ar.png#lightbox) 

La capture d’écran suivante montre l' [exemple **Tasky** localisé](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n) en Hébreu :

[![Capture d’écran de l’application Tasky en Hébreu](localization-images/rtl-he-sml.png)](localization-images/rtl-he.png#lightbox)

Le texte RTL est localisé à l’aide des fichiers **Strings. xml** de la même façon que le texte LTR.

## <a name="testing"></a>Test

Veillez à tester minutieusement les paramètres régionaux par défaut. Votre application se bloque si les ressources par défaut ne peuvent pas être chargées pour une raison quelconque (c’est-à-dire qu’elles sont manquantes).

### <a name="emulator-testing"></a>Test de l’émulateur

Pour obtenir des instructions sur la façon de définir un émulateur sur des paramètres régionaux spécifiques à l’aide de l’interface ADB, reportez-vous à la section test de Google [sur une émulateur Android](https://developer.android.com/guide/topics/resources/localization.html#testing) .

```shell
adb shell setprop persist.sys.locale fr-CA;stop;sleep 5;start
```

### <a name="device-testing"></a>Test des appareils

Pour effectuer un test sur un appareil, modifiez la langue dans l’application **paramètres** .

> [!TIP]
> Notez les icônes et l’emplacement des éléments de menu afin que vous puissiez rétablir le paramètre de langue d’origine.

## <a name="summary"></a>Récapitulatif

Cet article décrit les principes fondamentaux de la localisation d’applications Android à l’aide de la gestion des ressources intégrée. Vous pouvez en savoir plus sur les applications i18n et l10n pour iOS, Android et multiplateforme (y compris les applications Xamarin. Forms) dans [ce guide multiplateforme](~/cross-platform/app-fundamentals/localization.md).

## <a name="related-links"></a>Liens associés

- [Tasky (localisé dans le code) (exemple)](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n)
- [Localisation Android avec des ressources](https://developer.android.com/guide/topics/resources/localization.html)
- [Vue d’ensemble de la localisation multiplateforme](~/cross-platform/app-fundamentals/localization.md)
- [Xamarin. Forms, localisation](~/xamarin-forms/app-fundamentals/localization/index.md)
- [Localisation iOS](~/ios/app-fundamentals/localization/index.md)
