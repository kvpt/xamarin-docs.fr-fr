---
title: Localisation dans Xamarin. iOS
description: Ce document décrit les fonctionnalités de localisation iOS et l’utilisation de ces fonctionnalités dans les applications Xamarin. iOS. Il traite de la langue, des paramètres régionaux, des fichiers de chaînes, des images de lancement, etc.
ms.prod: xamarin
ms.assetid: DFD9EB4A-E536-18E4-C8FD-679BA9C836D8
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/28/2017
ms.openlocfilehash: 1f5f9e13607d672a6fdec5ed8fb116466973a260
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86938032"
---
# <a name="localization-in-xamarinios"></a>Localisation dans Xamarin. iOS

_Ce document traite des fonctionnalités de localisation du kit de développement logiciel (SDK) iOS et explique comment y accéder avec Xamarin._

Reportez-vous aux [encodages d’internationalisation](encodings.md) pour obtenir des instructions sur l’inclusion de jeux de caractères/pages de codes dans des applications qui doivent traiter des données non-Unicode.

## <a name="ios-platform-features"></a>fonctionnalités de la plateforme iOS

Cette section décrit quelques-unes des fonctionnalités de localisation d’iOS. Passez à la [section suivante](#localization-basics-in-ios) pour afficher du code spécifique et des exemples.

### <a name="language"></a>Langage

Les utilisateurs choisissent leur langue dans l’application **paramètres** . Ce paramètre affecte les chaînes et les images de langue affichées par le système d’exploitation et les applications.

Pour déterminer la langue utilisée dans une application, récupérez le premier élément de `NSBundle.MainBundle.PreferredLocalizations` :

```csharp
var lang = NSBundle.MainBundle.PreferredLocalizations[0];
```

Cette valeur sera un code de langue, par exemple `en` pour l’anglais, l’espagnol, le `es` `ja` japonais, etc. La valeur retournée est limitée à l’une des localisations prises en charge par l’application (à l’aide de règles de secours pour déterminer la meilleure correspondance).

Le code d’application n’a pas toujours besoin de vérifier cette valeur : Xamarin et iOS fournissent toutes deux des fonctionnalités permettant de fournir automatiquement la chaîne ou la ressource correcte pour la langue de l’utilisateur. Ces fonctionnalités sont décrites dans le reste de ce document.

> [!NOTE]
> Utilisez `NSLocale.PreferredLanguages` pour déterminer les préférences linguistiques de l’utilisateur, quelles que soient les localisations prises en charge par l’application. Les valeurs retournées par cette méthode ont changé dans iOS 9 ; Pour plus d’informations, consultez la [note technique TN2418](https://developer.apple.com/library/content/technotes/tn2418/_index.html) .

### <a name="locale"></a>Paramètres régionaux

Les utilisateurs choisissent leurs paramètres régionaux dans l’application **paramètres** . Ce paramètre affecte la façon dont les dates, les heures, les nombres et les devises sont mis en forme.

Cela permet aux utilisateurs de choisir s’ils voient des formats d’heure de 12 heures ou 24 heures, si leur séparateur décimal est une virgule ou un point, et l’ordre des jours, des mois et des années dans l’affichage de la date.

Avec Xamarin, vous avez accès aux classes iOS d’Apple ( `NSNumberFormatter` ) ainsi qu’aux classes .net dans System. Globalization. Les développeurs doivent évaluer ce qui est mieux adapté à leurs besoins, car différentes fonctionnalités sont disponibles dans chacune d’elles. En particulier, si vous récupérez et affichez des prix d’achat dans l’application à l’aide de StoreKit, vous devez utiliser les classes de mise en forme d’Apple pour les informations de prix retournées.

Les paramètres régionaux actuels peuvent être interrogés de deux manières :

- `NSLocale.CurrentLocale.LocaleIdentifier`
- `NSLocale.AutoUpdatingCurrentLocale.LocaleIdentifier`

La première valeur peut être mise en cache par le système d’exploitation et ne pas toujours refléter les paramètres régionaux actuellement sélectionnés par l’utilisateur. Utilisez la deuxième valeur pour obtenir les paramètres régionaux actuellement sélectionnés.

> [!NOTE]
> Mono (le Runtime .NET sur lequel Xamarin. iOS est basé) et les API iOS d’Apple ne prennent pas en charge des ensembles identiques de combinaisons langue/région.
> Pour cette raison, il est possible de sélectionner une combinaison langue/région dans l’application **paramètres** IOS qui ne mappe pas à une valeur valide en mono. Par exemple, si vous affectez la valeur anglais à la langue d’un iPhone et sa région à Espagne, les API suivantes produisent des valeurs différentes :
>
> - `CurrentThead.CurrentCulture`: en-US (API mono)
> - `CurrentThread.CurrentUICulture`: en-US (API mono)
> - `NSLocale.CurrentLocale.LocaleIdentifier`: en_ES (API Apple)
>
> Comme les utilisations mono `CurrentThread.CurrentUICulture` pour sélectionner des ressources et `CurrentThread.CurrentCulture` pour mettre en forme les dates et les devises, la localisation mono (par exemple, avec des fichiers. resx) risque de ne pas produire des résultats attendus pour ces combinaisons langue/région. Dans ces situations, utilisez les API d’Apple pour localiser le cas échéant.

### <a name="nscurrentlocaledidchangenotification"></a>NSCurrentLocaleDidChangeNotification

iOS génère un `NSCurrentLocaleDidChangeNotification` lorsque l’utilisateur met à jour ses paramètres régionaux. Les applications peuvent écouter cette notification pendant qu’elles sont en cours d’exécution et peuvent apporter des modifications appropriées à l’interface utilisateur.

## <a name="localization-basics-in-ios"></a>Notions de base de la localisation dans iOS

Les fonctionnalités suivantes d’iOS sont facilement exploitables dans Xamarin pour fournir à l’utilisateur des ressources localisées. Reportez-vous à l' [exemple TaskyL10n](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n) pour savoir comment implémenter ces idées.

### <a name="specifying-default-and-supported-languages-in-infoplist"></a>Spécification des langues par défaut et prises en charge dans info. plist

Dans [Technical Q&QA1828 : Comment iOS détermine la langue de votre application](https://developer.apple.com/library/content/qa/qa1828/_index.html), Apple explique comment iOS sélectionne une langue à utiliser dans une application. Les facteurs suivants ont un impact sur la langue affichée :

- Langues préférées de l’utilisateur (trouvées dans l’application **paramètres** )
- Les localisations regroupées avec l’application (dossiers. lproj)
- `CFBundleDevelopmentRegion`(Valeur**info. plist** spécifiant la langue par défaut de l’application)
- `CFBundleLocalizations`(Tableau**info. plist** spécifiant toutes les localisations prises en charge)

Comme indiqué dans le Forum aux questions techniques&A, `CFBundleDevelopmentRegion` représente la région et la langue par défaut d’une application. Si l’application ne prend pas explicitement en charge les langues préférées d’un utilisateur, elle utilisera la langue spécifiée par ce champ.

> [!IMPORTANT]
> iOS 11 applique ce mécanisme de sélection de langue plus rigoureusement que dans les versions précédentes du système d’exploitation. Pour cette raison, toute application iOS 11 qui ne déclare pas explicitement ses localisations prises en charge, soit en incluant les dossiers. lproj, soit en définissant une valeur pour `CFBundleLocalizations` – peut afficher une langue différente dans iOS 11 que dans iOS 10.

Si n' `CFBundleDevelopmentRegion` a pas été spécifié dans le fichier **info. plist** , les outils de génération Xamarin. iOS utilisent actuellement la valeur par défaut `en_US` . Bien que cela puisse changer dans une version ultérieure, cela signifie que la langue par défaut est l’anglais.

Pour vous assurer que votre application sélectionne une langue attendue, procédez comme suit :

- Spécifiez une langue par défaut. Ouvrez **info. plist** et utilisez la vue **source** pour définir une valeur pour la `CFBundleDevelopmentRegion` clé. dans XML, elle doit ressembler à ce qui suit :

```xml
<key>CFBundleDevelopmentRegion</key>
<string>es</string>
```

Cet exemple utilise « es » pour spécifier que lorsque aucune langue préférée de l’utilisateur n’est prise en charge, la valeur par défaut est l’espagnol.

- Déclarer toutes les localisations prises en charge. Dans **info. plist**, utilisez la vue **source** pour définir un tableau pour la `CFBundleLocalizations` clé ; dans XML, elle doit ressembler à ce qui suit :

```xml
<key>CFBundleLocalizations</key>
<array>
    <string>en</string>
    <string>es</string>
    ...
</array>
```

Les applications Xamarin. iOS qui ont été localisées à l’aide de mécanismes .NET tels que des fichiers. resx doivent également fournir ces valeurs **info. plist** .

Pour plus d’informations sur ces clés **info. plist** , jetez un coup d’œil à la [référence de clé de liste de propriétés d’informations](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/CoreFoundationKeys.html)Apple.

### <a name="getlocalizedstring-method"></a>Méthode GetLocalizedString

La `NSBundle.MainBundle.GetLocalizedString` méthode recherche du texte localisé qui a été stocké dans des fichiers **. Strings** dans le projet. Ces fichiers sont organisés par langue, dans des répertoires spécialement nommés avec un suffixe **. lproj** (Notez que la première lettre de l’extension est un « L » minuscule).

#### <a name="strings-file-locations"></a>emplacements des fichiers. Strings

- **Base. lproj** est le répertoire qui contient les ressources pour la langue par défaut.
  Il se trouve souvent à la racine du projet (mais peut également être placé dans le dossier **Resources** ).
- les répertoires ** &lt; Language &gt; . lproj** sont créés pour chaque langue prise en charge, généralement dans le dossier **Resources** .

Il peut y avoir plusieurs fichiers **. Strings** différents dans chaque répertoire de langue :

- **Localisable. Strings** : liste principale de texte localisé.
- **InfoPlist. Strings** : certaines clés spécifiques sont autorisées dans ce fichier pour traduire des éléments tels que le nom de l’application.
- ** \<storyboard-name> . Strings** : fichier facultatif qui contient des traductions pour les éléments d’interface utilisateur dans une table de montage séquentiel.

L' **action de génération** pour ces fichiers doit être **regrouper la ressource**.

#### <a name="strings-file-format"></a>format de fichier. Strings

La syntaxe des valeurs de chaîne localisées est :

```console
/* comment */
"key"="localized-value";
```

Vous devez placer dans une séquence d’échappement les caractères suivants dans les chaînes :

- `\"`cours
- `\\`vers
- `\n`caractère

Il s’agit d’un exemple des **chaînes es/localisables** (IE. Espagnol) à partir de l’exemple :

```console
"<new task>" = "<new task>";
"Task Details" = "Detalles de la tarea";
"Name" = "Nombre";
"task name" = "nombre de la tarea";
"Notes" = "Notas";
"other task info"= "otra información de tarea";
"Done" = "Completo";
"Save" = "Guardar";
"Delete" = "Eliminar";
```

### <a name="images"></a>Images

Pour localiser une image dans iOS :

1. Reportez-vous à l’image dans le code, par exemple :

    ```csharp
    UIImage.FromBundle("flag");
    ```

2. Placez le fichier image par défaut **flag.png** dans **base. lproj** (le répertoire du langage de développement natif).

3. Vous pouvez éventuellement placer des versions localisées de l’image dans des dossiers **. lproj** pour chaque langue (par exemple, **es. lproj**, **ja. lproj**). Utilisez le même nom de fichier **flag.png** dans chaque répertoire de langue.

Si aucune image n’est présente pour une langue particulière, iOS revient au dossier de langue native par défaut et charge l’image à partir de là.

#### <a name="launch-images"></a>Images de lancement

Utilisez les conventions d’affectation de noms standard pour les images de lancement (et XIB ou Storyboard pour les modèles iPhone 6) lors de leur placement dans les répertoires **. lproj** pour chaque langue.

```console
Default.png
Default@2x.png
Default-568h@2x.png
LaunchScreen.xib
```

### <a name="app-name"></a>Nom de l’application

Le fait de placer un fichier **InfoPlist. Strings** dans un répertoire **. lproj** vous permet de remplacer certaines valeurs du fichier **info. plist**de l’application, y compris le nom de l’application :

```console
"CFBundleDisplayName" = "LeónTodo";
```

Les autres clés que vous pouvez utiliser pour [localiser des chaînes spécifiques](https://developer.apple.com/library/ios/documentation/MacOSX/Conceptual/BPInternational/LocalizingYourApp/LocalizingYourApp.html#//apple_ref/doc/uid/10000171i-CH5-SW21) à l’application sont les suivantes :

- CFBundleName
- CFBundleShortVersionString
- NSHumanReadableCopyright

### <a name="dates-and-times"></a>Dates et heures

Bien qu’il soit possible d’utiliser les fonctions de date et d’heure .NET intégrées (avec le actuel `CultureInfo` ) pour mettre en forme les dates et les heures pour des paramètres régionaux, cela ignore les paramètres utilisateur spécifiques aux paramètres régionaux (qui peuvent être définis indépendamment de la langue).

Utilisez iOS `NSDateFormatter` pour produire une sortie qui correspond à la préférence des paramètres régionaux de l’utilisateur. L’exemple de code suivant illustre les options de mise en forme de base de la date et de l’heure :

```csharp
var date = NSDate.Now;
var df = new NSDateFormatter ();
df.DateStyle = NSDateFormatterStyle.Full;
df.TimeStyle = NSDateFormatterStyle.Long;
Debug.WriteLine ("Full,Long: " + df.StringFor(date));
df.DateStyle = NSDateFormatterStyle.Short;
df.TimeStyle = NSDateFormatterStyle.Short;
Debug.WriteLine ("Short,Short: " + df.StringFor(date));
df.DateStyle = NSDateFormatterStyle.Medium;
df.TimeStyle = NSDateFormatterStyle.None;
Debug.WriteLine ("Medium,None: " + df.StringFor(date));
```

Résultats pour l’anglais dans le États-Unis :

```console
Full,Long: Friday, August 7, 2015 at 10:29:32 AM PDT
Short,Short: 8/7/15, 10:29 AM
Medium,None: Aug 7, 2015
```

Résultats pour l’espagnol en Espagne :

```console
Full,Long: viernes, 7 de agosto de 2015, 10:26:58 GMT-7
Short,Short: 7/8/15 10:26
Medium,None: 7/8/2015
```

Pour plus d’informations, consultez la documentation sur les [formateurs de date](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/DataFormatting/Articles/dfDateFormatting10_4.html) Apple. Lors du test de la mise en forme de date et d’heure en respectant les paramètres régionaux, vérifiez les paramètres de langue et de **région** **iPhone** .

<a name="rtl"></a>

### <a name="right-to-left-rtl-layout"></a>Disposition de droite à gauche (RTL)

iOS fournit un certain nombre de fonctionnalités pour faciliter la création d’applications prenant en charge RTL :

- Utilisez la disposition automatique `leading` et les `trailing` attributs pour l’alignement du contrôle (qui correspond à gauche et à droite pour l’anglais, mais est inversé pour les langues RTL).
  Le [`UIStackView`](~/ios/user-interface/controls/uistackview.md) contrôle est particulièrement utile pour la disposition des contrôles en vue de la prise en charge de RTL.
- Utilisez `TextAlignment = UITextAlignment.Natural` pour l’alignement du texte (qui sera conservé pour la plupart des langues, mais juste pour RTL).
- `UINavigationController`retourne automatiquement le bouton précédent et inverse le sens du balayage.

Les captures d’écran suivantes illustrent l' [exemple Tasky localisé](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n) en arabe et en Hébreu (même si l’anglais a été entré dans les champs) :

[![Localisation en arabe](images/rtl-ar-sml.png)](images/rtl-ar.png#lightbox "Arabe")

[![Localisation en Hébreu](images/rtl-he-sml.png)](images/rtl-he.png#lightbox "Hébreu")

iOS inverse automatiquement le `UINavigationController` , et les autres contrôles sont placés à l’intérieur `UIStackView` ou alignés avec la disposition automatique.
Le texte RTL est localisé à l’aide de fichiers **. Strings** de la même façon que le texte LTR.

<a name="code"></a>

## <a name="localizing-the-ui-in-code"></a>Localisation de l’interface utilisateur dans le code

L’exemple [Tasky (localisé dans le code)](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n) montre comment localiser une application dans laquelle l’interface utilisateur est générée dans du code (plutôt que des XIB ou des storyboards).

### <a name="project-structure"></a>Structure du projet

![Arborescence des ressources](images/solution-code.png)

### <a name="localizablestrings-file"></a>Fichier. Strings localisable

Comme décrit ci-dessus, le format de fichier **localisable. Strings** se compose de paires clé-valeur. La clé décrit l’objectif de la chaîne et la valeur est le texte traduit à utiliser dans l’application.

Les localisations**es**espagnoles pour l’exemple sont indiquées ci-dessous :

```console
"<new task>" = "<new task>";
"Task Details" = "Detalles de la tarea";
"Name" = "Nombre";
"task name" = "nombre de la tarea";
"Notes" = "Notas";
"other task info"= "otra información de tarea";
"Done" = "Completo";
"Save" = "Guardar";
"Delete" = "Eliminar";
```

### <a name="performing-the-localization"></a>Réalisation de la localisation

Dans le code de l’application, où le texte d’affichage de l’interface utilisateur est défini (qu’il s’agisse du texte d’une étiquette ou de l’espace réservé d’une entrée, etc.), le code utilise la `GetLocalizedString` fonction IOS pour récupérer la traduction correcte à afficher :

```csharp
var localizedString = NSBundle.MainBundle.GetLocalizedString ("key", "optional");
someControl.Text = localizedString;
```

<a name="storyboard"></a>

## <a name="localizing-storyboard-uis"></a>Localisation d’interfaces utilisateur de Storyboard

L’exemple [Tasky (Storyboard localisé)](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10nStoryboard) montre comment localiser du texte sur des contrôles dans une table de montage séquentiel.

### <a name="project-structure"></a>Structure du projet

Le répertoire **base. lproj** contient la table de montage séquentiel et doit également contenir toutes les images utilisées dans l’application.

Les autres répertoires de langage contiennent un fichier **localisable. Strings** pour toutes les ressources de chaîne référencées dans le code, ainsi qu’un fichier **fichier mainstoryboard. Strings** qui contient des traductions de texte dans le Storyboard.

![Arborescence des ressources](images/solution-storyboard.png)

Les répertoires de langage doivent contenir une copie des images localisées, afin de remplacer celle présente dans **base. lproj**.

### <a name="object-id--localization-id"></a>ID d’objet/ID de localisation

Lorsque vous créez et modifiez des contrôles dans une table de montage séquentiel, sélectionnez chaque contrôle et vérifiez l’ID à utiliser pour la localisation :

- Dans Visual Studio pour Mac, elle se trouve dans le **panneau Propriétés** et est appelée **ID de localisation**.
- Dans Xcode, il s’agit de l' **ID d’objet**.

Cette valeur de chaîne a souvent une forme telle que « NF3-H8-xmR », comme illustré dans la capture d’écran suivante :

![Vue Xcode de la localisation des storyboards](images/xs-designer-localization-id.png)

Cette valeur est utilisée dans le fichier **. Strings** pour assigner automatiquement du texte traduit à chaque contrôle.

### <a name="mainstoryboardstrings"></a>Fichier mainstoryboard. Strings

Le format du fichier de traduction d’une table de montage séquentiel est semblable au fichier **localisable. Strings** , sauf que la clé (la valeur à gauche) ne peut pas être définie par l’utilisateur, mais qu’elle doit avoir un format très spécifique : `ObjectID.property` .

Dans l’exemple **fichier mainstoryboard. Strings** ci-dessous, vous pouvez voir que `UITextField` s ont une `placeholder` propriété Text qui peut être localisée ; `UILabel` ont une `text` propriété et le `UIButton` texte par défaut est défini à l’aide de `normalTitle` :

```console
"SXg-TT-IwM.placeholder" = "nombre de la tarea";
"Pqa-aa-ury.placeholder"= "otra información de tarea";
"zwR-D9-hM1.text" = "Detalles de la tarea";
"bAM-2j-Rzw.text" = "Notas";           /* Notes */
"NF3-h8-xmR.text" = "Completo";        /* Done */
"MWt-Ya-pMf.normalTitle" = "Guardar";  /* Save */
"IGr-pR-05L.normalTitle" = "Eliminar"; /* Delete */
```

> [!IMPORTANT]
> L’utilisation d’une table de montage séquentiel avec des classes de taille peut entraîner des traductions qui n’apparaissent pas dans l’application. Les [notes de publication de Xcode d’Apple](https://developer.apple.com/library/content/releasenotes/DeveloperTools/RN-Xcode/Chapters/Introduction.html) indiquent qu’une table de montage séquentiel ou une XIB ne se traduit pas correctement si trois choses sont vraies : elle utilise des classes de taille, la localisation de base et la cible de génération ont la valeur universel, et la génération cible iOS 7,0. Le correctif consiste à dupliquer votre fichier de chaînes de Storyboard dans deux fichiers identiques : **fichier mainstoryboard ~ iPhone. Strings** et **fichier mainstoryboard ~ iPad. Strings**, comme indiqué dans la capture d’écran suivante :
>
> ![Fichiers de chaînes](images/xs-dup-strings.png)

<a name="appstore"></a>

## <a name="app-store-listing"></a>Liste de l’App Store

Suit le FAQ d’Apple sur la localisation de l' [App Store](https://itunespartner.apple.com/en/apps/faq/App%20Store_Localization) pour entrer des traductions pour chaque pays sur lequel votre application est en vente. Notez que les traductions s’affichent uniquement si votre application contient également un répertoire **. lproj** localisé pour la langue.

## <a name="summary"></a>Résumé

Cet article décrit les principes fondamentaux de la localisation d’applications iOS à l’aide des fonctionnalités intégrées de gestion des ressources et de Storyboard.

Vous pouvez en savoir plus sur i18n et l10n pour les applications iOS, Android et multiplateforme (y compris Xamarin. Forms) dans [ce guide multiplateforme](~/cross-platform/app-fundamentals/localization.md).

## <a name="related-links"></a>Liens associés

- [Tasky (localisé dans le code) (exemple)](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n)
- [Tasky (Storyboard localisé) (exemple)](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10nStoryboard)
- [Guide de Localisation Apple](https://developer.apple.com/library/ios/documentation/MacOSX/Conceptual/BPInternational/InternationalizingYourUserInterface/InternationalizingYourUserInterface.html)
- [Vue d’ensemble de la localisation multiplateforme](~/cross-platform/app-fundamentals/localization.md)
- [Localisation Xamarin.Forms](~/xamarin-forms/app-fundamentals/localization/index.md)
- [Localisation Android](~/android/app-fundamentals/localization.md)
