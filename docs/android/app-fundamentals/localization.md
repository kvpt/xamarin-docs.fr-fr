---
title: Localisation Android
description: Ce document présente les fonctionnalités de localisation du SDK Android et comment y accéder avec Xamarin.
ms.prod: xamarin
ms.assetid: D1277939-A1E8-468E-B136-820D816AF853
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: f6a7f8859340dcc8e48b6a4e6f56847168f4b71e
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67829759"
---
# <a name="android-localization"></a>Localisation Android

_Ce document présente les fonctionnalités de localisation du SDK Android et comment y accéder avec Xamarin._

## <a name="android-platform-features"></a>Fonctionnalités de la plateforme Android

Cette section décrit les fonctionnalités de localisation principal d’Android. Passez à la [section suivante](#basics) pour afficher des fichiers et des exemples de code spécifique.

### <a name="locale"></a>Paramètres régionaux

Les utilisateurs choisir leur langue dans **Paramètres > langage & entrée**. Cette sélection détermine la langue affichée et les paramètres régionaux utilisés (par exemple). pour la date et de mise en forme de nombre).

Les paramètres régionaux actuels peuvent être interrogée via le contexte actuel `Resources`:

```csharp
var lang = Resources.Configuration.Locale; // eg. "es_ES"
```

Cette valeur sera un identificateur de paramètres régionaux qui contient un code de langue et un code de paramètres régionaux, séparés par un trait de soulignement. Pour référence, voici une [liste des paramètres régionaux Java](https://www.oracle.com/technetwork/java/javase/locales-137662.html) et [Android pris en charge les paramètres régionaux via StackOverflow](https://stackoverflow.com/questions/7973023/what-is-the-list-of-supported-languages-locales-on-android).

Voici quelques exemples usuels :

* `en_US` pour l’anglais (États-Unis)
* `es_ES` pour l’espagnol (Espagne)
* `ja_JP` pour le japonais (Japon)
* `zh_CN` pour le chinois (Chine)
* `zh_TW` pour le chinois (Taïwan)
* `pt_PT` portugais (Portugal)
* `pt_BR` portugais (Brésil)

### <a name="localechanged"></a>LOCALE_CHANGED

Android génère `android.intent.action.LOCALE_CHANGED` lorsque l’utilisateur modifie la sélection de la langue.

Activités peuvent choisir de gérer cette situation en définissant le `android:configChanges` attribut sur l’activité, comme suit :

```csharp
[Activity (Label = "@string/app_name", MainLauncher = true, Icon="@drawable/launcher",
        ConfigurationChanges = ConfigChanges.Locale | ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
```

<a name="basics" />

## <a name="internationalization-basics-in-android"></a>Principes fondamentaux de l’internationalisation dans Android

Stratégie de localisation d’Android a les clés suivantes :

- Dossiers de ressources pour contenir des chaînes localisées, d’images et d’autres ressources.

- `GetText` méthode, qui est utilisée pour récupérer les chaînes localisées dans le code

- `@string/id` dans les fichiers AXML, pour placer automatiquement les chaînes localisées dans des dispositions.

### <a name="resource-folders"></a>Dossiers de ressources

Les applications Android gérer la plupart des contenus dans les dossiers de ressources, telles que :

* **disposition** -contient les fichiers de disposition AXML.
* **drawable** -contient des images et autres ressources drawable.
* **valeurs** -contient des chaînes.
* **brutes** -contient les fichiers de données.

La plupart des développeurs connaissent déjà l’utilisation de **PPP** suffixes sur le **drawable** directory pour fournir plusieurs versions d’une image, Android vous permettant de choisir la version correcte pour chaque appareil. Le même mécanisme permet de fournir plusieurs traductions de langage ajoutant des répertoires de ressources avec des identificateurs de langue et culture.

![Capture d’écran de dossiers de ressources/drawable et ressources/valeurs pour plusieurs identificateurs de culturels](localization-images/resources.png)

> [!NOTE]
> Lors de la spécification d’un langage de niveau supérieur comme `es` uniquement deux caractères sont requis ; Toutefois, lorsque vous spécifiez un ensemble des paramètres régionaux, le format de nom de répertoire requiert un tiret et les minuscules **r** pour séparer les deux parties, par exemple **pt-rBR** ou **zh-rCN**. Comparez cela à la valeur retournée dans le code, qui comporte un trait de soulignement (par exemple). `pt_BR`). Ces deux éléments sont différents pour la valeur .NET `CultureInfo` classe utilise, ce qui a un tiret uniquement (par exemple). `pt-BR`). Gardez ces différences à l’esprit lorsque vous travaillez sur plusieurs plateformes de Xamarin.

#### <a name="stringsxml-file-format"></a>Format de fichier Strings.Xml

Une commande localisée **valeurs** répertoire (par exemple). **valeurs-es** ou **valeurs-pt-rBR**) doit contenir un fichier appelé **Strings.xml** qui contiendra le texte traduit pour ces paramètres régionaux.

Chaque chaîne traduisible est un élément XML avec la ressource ID spécifié en tant que le `name` attribut et la chaîne traduite en tant que la valeur :

```xml
<string name="app_name">TaskyL10n</string>
```

Vous devez échapper selon les règles XML normales et le `name` doit être un ID de ressource Android valide (sans espaces ou des tirets). Voici un exemple du fichier de chaînes (en anglais) par défaut pour l’exemple :

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

Le répertoire espagnol **valeurs-es** contient un fichier portant le même nom (**Strings.xml**) qui contient les traductions :

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

![Capture d’écran de plusieurs valeurs de dossiers, chacun contenant un fichier Strings.xml](localization-images/values.png)

Avec la configuration de fichiers de chaînes, les valeurs traduites peuvent être référencés dans les dispositions et code.

### <a name="axml-layout-files"></a>Fichiers de disposition AXML

Pour référencer des chaînes localisées dans des fichiers de disposition, utilisez le `@string/id` syntaxe. Cet extrait de code XML à partir de l’exemple montre `text` définition avec des propriétés localisées (autres certains attributs ont été omis) des ID de ressource :

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

### <a name="gettext-method"></a>GetText (méthode)

Pour récupérer les chaînes traduites dans le code, utilisez le `GetText` (méthode) et passez l’ID de ressource :

```csharp
var cancelText = Resources.GetText (Resource.String.taskcancel);
```

#### <a name="quantity-strings"></a>Chaînes de quantité

Ressources de type chaîne Android vous permettent également de créer *les chaînes de quantité* qui permettent des traducteurs fournir des traductions différentes pour des quantités différentes, telles que :

* « Il existe 1 tâche gauche. »
* « Il existe 2 tâches toujours pour faire. »

(au lieu d’un générique « il existe ou les tâches n gauche »).

Dans le **Strings.xml**

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

Pour afficher l’utilisation de la chaîne complète la `GetQuantityString` méthode, en passant l’ID de ressource et la valeur à afficher (qui est transmis à deux reprises). Le deuxième paramètre est utilisé par Android pour déterminer *qui* `quantity` chaîne à utiliser, le troisième paramètre est la valeur réellement substituée dans la chaîne (les deux sont requis).

```csharp
var translated = Resources.GetQuantityString (
                    Resource.Plurals.numberOfTasks, taskcount, taskcount);`
```

Valide `quantity` commutateurs sont :

* zéro
* one
* Deux
* quelques
* many
* other

Ils sont décrits plus en détail dans le [docs Android](https://developer.android.com/guide/topics/resources/string-resource.html#Plurals). Si une langue donnée ne requiert pas de Gestion « spécial », celles `quantity` chaînes va être ignorées (par exemple, en anglais utilise uniquement `one` et `other`; en spécifiant un `zero` chaîne n’a aucun effet, il ne sera pas utilisé).

### <a name="images"></a>Images

Images localisées suivent les mêmes règles sous forme de fichiers de chaînes : toutes les images référencées dans l’application doivent être placés dans **drawable** répertoires par conséquent, il est une solution de secours.

Les images spécifiques puis doivent être placés dans les dossiers dessinables qualifiés tel que **drawable es** ou **drawable ja** (spécificateurs de PPP peuvent également être ajoutés).

Dans cette capture d’écran, les quatre images sont enregistrées dans le **drawable** directory, mais une seule, **flag.png**, a localisé copies dans d’autres répertoires.

![Capture d’écran de plusieurs dossiers dessinables, chacun contenant un ou plusieurs fichiers .png localisée](localization-images/drawable.png)


#### <a name="other-resource-types"></a>Autres Types de ressources

Vous pouvez également fournir des autres types d’alternative, les ressources spécifiques au langage, y compris les dispositions, des animations et des fichiers bruts. Cela signifie que vous pouvez fournir une disposition d’écran spécifique pour un ou plusieurs de vos langages cible, par exemple, vous pouvez créer une disposition en particulier pour l’allemand permettant d’étiquettes de texte très longues.

Android 4.2 a introduit la prise en charge de [de droite à gauche (les) langues](http://android-developers.blogspot.fr/2013/03/native-rtl-support-in-android-42.html) si vous définissez le paramètre d’application `android:supportsRtl="true"`. Le qualificateur de ressource `"ldrtl"` peut être inclus dans un nom de répertoire pour contenir des dispositions personnalisées qui sont conçues pour l’affichage de droite à gauche.

Pour plus d’informations sur les noms de répertoire ressource et de secours, reportez-vous à la documentation Android pour [fournir des ressources alternatives](https://developer.android.com/guide/topics/resources/providing-resources.html#AlternativeResources).


### <a name="app-name"></a>Nom de l’application

Le nom de l’application est facile à localiser à l’aide un `@string/id` dans pour la `MainLauncher` activité :

```csharp
[Activity (Label = "@string/app_name", MainLauncher = true, Icon="@drawable/launcher",
    ConfigurationChanges =  ConfigChanges.Orientation | ConfigChanges.Locale)]
```

### <a name="right-to-left-rtl-languages"></a>Langues de droite à gauche (DÀG)

Android 4.2 et versions ultérieures prend complètement en charge les dispositions de droite à gauche, décrites en détail dans le [blog prend en charge Native de droite à gauche](http://android-developers.blogspot.dk/2013/03/native-rtl-support-in-android-42.html).

Lorsque vous utilisez Android 4.2 (niveau d’API 17) et l’alignement plus récente, les valeurs peuvent être spécifiées avec `start` et `end` au lieu de `left` et `right` (par exemple `android:paddingStart`). Il existe également des nouvelles API comme `LayoutDirection`, `TextDirection`, et `TextAlignment` pour aider à créer des écrans qui s’adaptent pour les lecteurs de droite à gauche.

La capture d’écran suivante montre le [localisée **Tasky** exemple](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n) en arabe :

[![Capture d’écran de l’application Tasky en arabe](localization-images/rtl-ar-sml.png)](localization-images/rtl-ar.png#lightbox) 

La capture d’écran suivante montre le [localisée **Tasky** exemple](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n) en hébreu :

[![Capture d’écran de l’application Tasky en hébreu](localization-images/rtl-he-sml.png)](localization-images/rtl-he.png#lightbox)

Texte de droite à gauche est localisé à l’aide de **Strings.xml** fichiers dans la même façon que le texte de la rétention à long terme.

## <a name="testing"></a>Test

Veillez à tester soigneusement les paramètres régionaux par défaut. Votre application se bloque si les ressources par défaut ne peut pas être chargés pour une raison quelconque (par exemple, ils sont manquants).

### <a name="emulator-testing"></a>Émulateur de test

Reportez-vous à Google [test sur un émulateur Android](https://developer.android.com/guide/topics/resources/localization.html#testing) section pour obtenir des instructions sur la configuration d’un émulateur pour des paramètres régionaux spécifiques à l’aide de l’interpréteur de commandes ADB.

```shell
adb shell setprop persist.sys.locale fr-CA;stop;sleep 5;start
```

### <a name="device-testing"></a>Test de l’appareil

Pour tester sur un appareil, modifiez la langue dans le **paramètres** application.

> [!TIP]
> Rendre une note des icônes et l’emplacement des éléments de menu afin que vous pouvez revenir à la langue à la configuration d’origine.


## <a name="summary"></a>Récapitulatif

Cet article aborde les principes fondamentaux de la localisation d’applications Android à l’aide de la gestion de ressources intégrées. Vous pouvez en savoir plus sur i18n et L10n pour iOS, Android et applications inter-plateformes (y compris Xamarin.Forms) dans [ce guide inter-plateformes](~/cross-platform/app-fundamentals/localization.md).



## <a name="related-links"></a>Liens associés

- [Tasky (localisé dans le code) (exemple)](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n)
- [Localisation des ressources de Android](https://developer.android.com/guide/topics/resources/localization.html)
- [Vue d’ensemble de la localisation inter-plateformes](~/cross-platform/app-fundamentals/localization.md)
- [Localisation de Xamarin.Forms](~/xamarin-forms/app-fundamentals/localization/index.md)
- [Localisation iOS](~/ios/app-fundamentals/localization/index.md)
