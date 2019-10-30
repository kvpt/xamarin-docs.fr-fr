---
title: Utilisation de la localisation Watchos dans Xamarin
description: Ce document décrit comment localiser des applications Watchos générées avec Xamarin. Il aborde les applications de surveillance, les extensions de visionnement, les chaînes dans le code, le texte de Storyboard, les tests, etc.
ms.prod: xamarin
ms.assetid: 55834877-757B-4860-AF2F-933A948BE38D
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 82b739697705ac4c90390a36405d755a5f523159
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028402"
---
# <a name="working-with-watchos-localization-in-xamarin"></a>Utilisation de la localisation Watchos dans Xamarin

_Adaptation de vos applications Watchos pour plusieurs langues_

![](localization-images/both-languages-sml.png "Apple Watch displaying localized content")

les applications Watchos sont localisées à l’aide des méthodes iOS standard :

- Utilisation de l' **ID de localisation** sur les éléments de Storyboard,
- fichiers **. Strings** associés à la table de montage séquentiel et
- Fichiers **. Strings localisables** pour le texte utilisé dans le code.

Les storyboards et les ressources par défaut se trouvent dans un répertoire de **base** , et les traductions spécifiques à la langue et d’autres ressources sont stockées dans les répertoires **. lproj** .
le système d’exploitation iOS et Watch utilise automatiquement la sélection de la langue de l’utilisateur pour charger les chaînes et les ressources correctes.

Étant donné qu’une application Apple Watch a deux parties : l’application de surveillance et les ressources de chaîne localisées, elles sont requises dans deux emplacements, selon la façon dont elles sont utilisées.

Le texte et les ressources localisés seront *différents* dans l’application Watch et l’extension Watch.

## <a name="watch-app"></a>Application Watch

L’application Watch contient la table de montage séquentiel qui décrit l’interface utilisateur de l’application. Tous les contrôles (tels que `Label` et `Image`) qui prennent en charge la localisation ont un **ID de localisation**.

Chaque répertoire **. lproj** spécifique à une langue doit contenir des fichiers **. Strings** avec les traductions pour chaque élément (à l’aide de l' **ID de localisation**), ainsi que des images référencées par le Storyboard.

## <a name="watch-extension"></a>Extension Watch

L’extension Watch est l’emplacement où votre code d’application s’exécute. Tout texte affiché à l’utilisateur à partir du code doit être localisé dans l’extension et non dans l’application Watch.

L’extension doit également contenir des répertoires **. lproj** spécifiques à la langue, mais les fichiers **. Strings** requièrent uniquement des traductions pour le texte utilisé dans votre code.

## <a name="globalizing-the-watch-solution"></a>Globalisation de la solution Watch

La globalisation est le processus qui consiste à rendre une application localisable.
Pour les applications Watch, cela signifie que vous concevez le Storyboard avec différentes longueurs de texte à l’esprit, en veillant à ce que chaque disposition d’écran s’ajuste de manière appropriée en fonction du texte affiché. Vous devez également vous assurer que toutes les chaînes référencées dans le code de l’extension Watch peuvent être traduites à l’aide de la méthode `LocalizedString`.

### <a name="watch-app"></a>Application Watch

Par défaut, l’application Watch n’est pas configurée pour la localisation. Vous devez déplacer le fichier de Storyboard par défaut et créer d’autres répertoires pour vos traductions :

1. Créez le répertoire **base. lproj** et déplacez l' **interface. Storyboard** dans celui-ci.

2. Créez des répertoires **\<language >. lproj** pour chaque langue que vous souhaitez prendre en charge.

3. Les répertoires **. lproj** doivent contenir un fichier texte **interface. Strings** (le nom de fichier doit correspondre au nom du tableau). Vous pouvez éventuellement placer les images qui nécessitent une localisation dans ces répertoires.

Le projet d’application Watch ressemble à ceci après que ces modifications ont été apportées (seuls les fichiers de langue anglais et espagnol ont été ajoutés) :

  ![](localization-images/watchapp-solution.png "The watch app project with English and Spanish language files")

#### <a name="storyboard-text"></a>Texte de Storyboard

Lorsque vous modifiez la table de montage séquentiel, sélectionnez chaque élément et notez l' **ID de localisation** qui apparaît dans le panneau **Propriétés** :

  [![](localization-images/storyboard-sml.png "The Localization ID that appears in the Properties pad")](localization-images/storyboard.png#lightbox)

Dans le dossier **base. lproj** , créez des paires clé-valeur comme indiqué ci-dessous, où la clé est formée par l' **ID de localisation** et un nom de propriété sur le contrôle, joint par un point (`.`).

```csharp
"AgC-eL-Hgc.title" = "WatchL10nEN"; // interface controller title
"0.text" = "Welcome to WatchL10n"; // Welcome
"1.text" = "Language settings are in Apple Watch App"; // How to change language
"2.title" = "Greetings"; // Greeting
"6.title" = "Detail";
"39.text" = "Second screen";
```

Notez dans cet exemple qu’un **ID de localisation** peut être une chaîne de nombre simple (par exemple, « 0 », « 1 », etc.) ou une chaîne plus complexe (telle que « AgC-eL-HGC »). les contrôles `Label` ont une propriété `Text` et `Button`ont une propriété `Title`, qui est reflétée dans la façon dont leurs valeurs localisées sont définies, veillez à utiliser le nom de propriété en minuscules comme indiqué dans l’exemple ci-dessus.

Lorsque le Storyboard est rendu sur la montre, les valeurs correctes sont automatiquement extraites et affichées en fonction de la langue sélectionnée par l’utilisateur.

#### <a name="storyboard-images"></a>Images de Storyboard

L’exemple de solution comprend également une image **gradient@2x.png** dans chaque dossier de langue. Cette image peut être différente pour chaque langue (par exemple, elle peut contenir du texte incorporé qui doit être traduit, ou utiliser des iconographie localisés.

Il vous suffit de définir la propriété **image** de l’image dans la table de montage séquentiel et l’image correcte sera restituée sur l’espion en fonction de la langue sélectionnée par l’utilisateur.

![](localization-images/storyboard-image.png "Set the images Image property in the storyboard")

Remarque : étant donné que tous les espions Apple ont des affichages de retine, seule la version **@2x** de l’image est requise. Vous n’avez pas besoin de spécifier **@2x** dans le Storyboard.

### <a name="watch-extension"></a>Extension Watch

L’extension Watch requiert une structure de répertoires similaire pour prendre en charge la localisation, mais il n’existe aucune table de montage séquentiel. Les chaînes localisées dans l’extension sont uniquement celles référencées par C# le code.

![](localization-images/watchextension-solution.png "The watch extension directory structure to support localization")

#### <a name="strings-in-code"></a>Chaînes dans le code

Le fichier **localisable. Strings** a une structure légèrement différente de celle qui était associée à une table de montage séquentiel. Dans ce cas, nous pouvons choisir n’importe quelle chaîne de « clé ». La recommandation d’Apple est d’utiliser une clé qui reflète le texte réel qui serait affiché dans la langue par défaut :

```csharp
"Breakfast time" = "Breakfast time!"; // morning
"Lunch time" = "Lunch time!"; // midday
"Dinner time" = "Dinner time!"; // evening
"Bed time" = "Bed time!"; // night
```

La méthode `NSBundle.MainBundle.LocalizedString` est utilisée pour résoudre les chaînes en leurs équivalents traduits, comme indiqué dans le code ci-dessous.

```csharp
var display = "Breakfast time";
var localizedDisplay =
  NSBundle.MainBundle.LocalizedString (display, comment:"greeting");
displayText.SetText (localizedDisplay);
```

#### <a name="images-in-code"></a>Images dans le code

Les images qui sont remplies par le code peuvent être définies de deux façons.

1. Vous pouvez modifier un contrôle `Image` en définissant sa valeur sur le nom de chaîne d’une image qui existe déjà dans l’application Watch, par exemple

    ```csharp
    displayImage.SetImage("gradient"); // image in Watch App (as shown above)
    ```

2. Vous pouvez déplacer une image de l’extension vers l’espion à l’aide de `FromBundle` et l’application choisit automatiquement l’image correcte pour la sélection de la langue de l’utilisateur. Dans l’exemple de solution, il existe une image **language@2x.png** dans chaque dossier de langue et elle est affichée sur `DetailController` à l’aide du code suivant :

    ```csharp
    using (var image = UIImage.FromBundle ("language")) {
        displayImage.SetImage (image);
    }
    ```

    Notez que vous n’avez pas besoin de spécifier le **@2x** lorsque vous faites référence au nom de fichier de l’image.

La seconde méthode s’applique également si vous téléchargez une image à partir d’un serveur distant pour effectuer un rendu sur la montre. Toutefois, dans ce cas, vous devez vous assurer que l’image que vous téléchargez est correctement localisée en fonction des préférences de l’utilisateur.

## <a name="localization"></a>Localisation

Une fois que vous avez configuré votre solution, les traducteurs doivent traiter vos fichiers **. Strings** et vos images pour chaque langue que vous souhaitez prendre en charge.

Vous pouvez créer autant de répertoires **. lproj** que nécessaire (un pour chaque langue prise en charge). Elles sont nommées à l’aide de codes de langue, tels que **fr**, **es**, **de**, **ja**, **pt-br**, etc. (pour l’anglais, l’espagnol, l’allemand, le japonais et le portugais (Brésil), respectivement).

L’exemple joint utilise des traductions (générées par l’ordinateur) pour montrer comment localiser une application Watchos.

### <a name="watch-app"></a>Application Watch

Ces valeurs sont utilisées pour traduire l’interface utilisateur définie dans la table de montage séquentiel de l’application Watch. La valeur de *clé* est une combinaison de l’ID de **localisation** de chaque contrôle de Storyboard et de la propriété en cours de traduction.

Il est recommandé d’ajouter des commentaires contenant le texte d’origine au fichier afin que les traducteurs sachent ce que doit être la traduction.

#### <a name="eslprojinterfacestrings"></a>es. lproj/interface. Strings

Les chaînes de langue espagnole (traduites par l’ordinateur) pour la table de montage séquentiel sont indiquées ci-dessous. Il est utile d’ajouter des commentaires à chaque ligne, car il est difficile de savoir à quoi l' **ID de localisation** fait référence autrement :

```csharp
"AgC-eL-Hgc.title" = "Spanish"; // app screen heading
"0.text" = "Bienvenido a WatchL10n"; // Welcome to WatchL10n
"1.text" = "Ajustes de idioma están en Apple Watch App"; // Change the language in the Apple Watch App
"2.title" = "Saludos"; // Greetings
"6.title" = "2nd"; // second screen heading
"39.text" = "Segunda pantalla"; // second screen
```

### <a name="watch-extension"></a>Extension Watch

Ces valeurs sont utilisées dans le code pour traduire des informations avant d’être affichées à l’utilisateur. La *clé* est sélectionnée par le développeur lors de l’écriture du code et contient généralement la chaîne réelle à traduire.

#### <a name="eslprojlocalizablestrings-file"></a>s. lproj/localisable. Strings (fichier)

Chaînes de langue Spansish (ordinateur traduit) :

```csharp
"Breakfast time" = "la hora del desayuno"; // morning
"Lunch time" = "hora de comer"; // midday
"Dinner time" = "hora de la cena"; // evening
"Bed time" = "la hora de dormir"; // night
```

## <a name="testing"></a>Test

La méthode de modification des préférences linguistiques diffère entre le simulateur et les appareils physiques.

### <a name="simulator"></a>Simulateur

Dans le simulateur, sélectionnez la langue à tester à l’aide de l’application **paramètres** iOS (l’icône des engrenages gris dans l’écran d’accueil du simulateur).

  ![](localization-images/sim-settings-sml.png "The iOS Settings app Localization settings")

### <a name="watch-device"></a>Observer l’appareil

Quand vous effectuez un test avec un espion, changez la langue de l’espion dans l’application **Apple Watch** sur l’iPhone couplé.

  ![](localization-images/phone-settings-sml.png "Change the watch's language in the Apple Watch app on the paired iPhone")

## <a name="related-links"></a>Liens associés

- [WatchLocalization (exemple)](https://developer.xamarin.com//samples/monotouch/watchOS/WatchLocalization/)
