---
title: Utilisation de watchOS localisation dans Xamarin
description: Ce document décrit comment localiser des applications watchOS avec Xamarin. Il traite des applications cadran, extensions espion, chaînes dans le code, animation de texte, le test et bien plus encore.
ms.prod: xamarin
ms.assetid: 55834877-757B-4860-AF2F-933A948BE38D
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/17/2017
ms.openlocfilehash: 10f240a8e245f24d4b8f646eb972cbe21d28b75c
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70289935"
---
# <a name="working-with-watchos-localization-in-xamarin"></a>Utilisation de watchOS localisation dans Xamarin

_Adaptation de vos applications watchOS pour plusieurs langues_

![](localization-images/both-languages-sml.png "Apple Watch affichant le contenu localisé")

applications watchOS sont localisées à l’aide des méthodes standard iOS :

- À l’aide de **ID de localisation** sur les éléments de la table de montage séquentiel,
- **.Strings** fichiers associés à la table de montage séquentiel, et
- **Localizable.Strings** fichiers texte utilisé dans le code.

Les tables de montage séquentiel par défaut et les ressources se trouvent dans un **Base** directory et les traductions spécifiques au langage et autres ressources sont stockées dans **.lproj** répertoires.
iOS et le système d’exploitation Watch utilisera automatiquement sélection de la langue de l’utilisateur pour charger les chaînes et les ressources.

Car une application Apple Watch a deux parties - application Watch et Extension Watch - chaîne localisée des ressources sont nécessaires dans deux emplacements, selon la façon dont ils sont utilisés.

Le texte localisé et les ressources seront *différents* dans l’application watch et l’extension watch.

## <a name="watch-app"></a>Application Watch

L’application watch contient la table de montage séquentiel qui décrit l’interface utilisateur de l’application. Tous les contrôles (tels que `Label` et `Image`) que la prise en charge de localisation ont un **ID de localisation**.

Chaque spécifiques au langage **.lproj** répertoire doit contenir **.strings** fichiers avec les traductions pour chaque élément (à l’aide de la **ID de localisation**), ainsi que des images référencé par la table de montage séquentiel.

## <a name="watch-extension"></a>Extension Watch

L’extension watch est sur lequel s’exécute le code de votre application. N’importe quel texte qui s’affiche à l’utilisateur à partir du code doit être localisée dans l’extension et non dans l’application watch.

L’extension doit également contenir spécifiques au langage **.lproj** répertoires, mais la **.strings** fichiers nécessitent uniquement des traductions pour le texte qui est utilisé dans votre code.

## <a name="globalizing-the-watch-solution"></a>Globalisation de la Solution de surveillance

La globalisation est le processus de création d’une application localisable.
Pour les applications cadran qu'il convient donc de la table de montage séquentiel avec des longueurs de texte différentes à l’esprit, en garantissant la disposition de l’écran ajuste correctement selon que le texte s’affiche. Vous devez également vous assurer toutes les chaînes référencés dans le code d’extension espion peuvent être traduites à l’aide de la `LocalizedString` (méthode).

### <a name="watch-app"></a>Application Watch

Par défaut, l’application Apple watch n’est pas configurée pour la localisation. Vous devez déplacer le fichier de la table de montage séquentiel par défaut et créer quelques autres répertoires pour vos traductions :

1. Créer **Base.lproj** directory et déplacer le **Interface.storyboard** dedans.

2. Créez les répertoires  **Language>.lprojpourchaquelanguequevoussouhaitezprendreencharge\<** .

3. Le **.lproj** répertoires doivent contenir un **Interface.strings** fichier texte (le nom de fichier doit correspondre à nom de la storboard). Vous pouvez éventuellement placer toutes les images doivent donc être localisées dans ces répertoires.

Le projet d’application watch ressemble à ceci une fois ces modifications ont été apportées (uniquement en anglais et espagnol language fichiers ont été ajoutés) :

  ![](localization-images/watchapp-solution.png "Le projet d’application de surveillance avec des fichiers de langue anglais et espagnol")

#### <a name="storyboard-text"></a>Texte de la table de montage séquentiel

Lorsque vous modifiez le plan conceptuel, sélectionnez chaque élément et les avis le **ID de localisation** qui s’affiche dans le **propriétés** panneau :

  [![](localization-images/storyboard-sml.png "L’ID de localisation qui s’affiche dans le panneau Propriétés")](localization-images/storyboard.png#lightbox)

Dans le **Base.lproj** dossier, créer des paires clé-valeur comme illustré ci-dessous, où la clé est formée par la **ID de localisation** et un nom de propriété sur le contrôle, jointe à un point (`.`).

```csharp
"AgC-eL-Hgc.title" = "WatchL10nEN"; // interface controller title
"0.text" = "Welcome to WatchL10n"; // Welcome
"1.text" = "Language settings are in Apple Watch App"; // How to change language
"2.title" = "Greetings"; // Greeting
"6.title" = "Detail";
"39.text" = "Second screen";
```

Dans cet exemple, notez qu’un **ID de localisation** peut être une chaîne de nombres simple (par exemple). « 0 », « 1 », etc.) ou une chaîne plus complexe (par exemple, « AgC-eL-Hgc »). `Label` les contrôles ont un `Text` propriété et `Button`s ont un `Title` propriété, qui est reflétée dans la configuration de leurs valeurs localisées sont - veillez à utiliser le nom de propriété en minuscules comme indiqué dans l’exemple ci-dessus.

Lorsque la table de montage séquentiel est rendu sur la surveillance, les valeurs correctes sont automatiquement extraits et affichées en fonction de la langue sélectionnée par l’utilisateur.

#### <a name="storyboard-images"></a>Images de la table de montage séquentiel

L’exemple de solution inclut également un **gradient@2x.png** image dans chaque dossier de langue. Cette image peut être différente pour chaque langue (par exemple). Il peut comporter de texte doit traduire, ou utilisez localisé iconographie).

Il suffit de définir l’image **Image** propriété dans la table de montage séquentiel et de l’image correcte sera rendue sur la surveillance en fonction de la langue sélectionnée par l’utilisateur.

![](localization-images/storyboard-image.png "Définir les images de la propriété Image dans la table de montage séquentiel")

Remarque : étant donné que tous les montres Apple ont Retina, uniquement le **@2x** version de l’image est obligatoire. Vous n’avez pas besoin de spécifier **@2x** dans le storyboard.

### <a name="watch-extension"></a>Extension Watch

L’extension watch nécessite une structure de répertoires similaire pour prendre en charge la localisation, cependant il n’existe aucun plan conceptuel. Les chaînes localisées dans l’extension sont uniquement ceux référencés par C# code.

![](localization-images/watchextension-solution.png "La structure de répertoire d’extension watch pour prendre en charge de la localisation")

#### <a name="strings-in-code"></a>Chaînes dans le Code

Le **Localizable.strings** fichier possède une structure légèrement différente que s’il a été associé à une table de montage séquentiel. Dans ce cas, nous pouvons choisir n’importe quelle chaîne « clé » ; Il est recommandé de d’Apple pour utiliser une clé qui reflète le texte qui s’affiche dans la langue par défaut :

```csharp
"Breakfast time" = "Breakfast time!"; // morning
"Lunch time" = "Lunch time!"; // midday
"Dinner time" = "Dinner time!"; // evening
"Bed time" = "Bed time!"; // night
```

Le `NSBundle.MainBundle.LocalizedString` méthode est utilisée pour résoudre les chaînes en leurs équivalents traduites, comme indiqué dans le code ci-dessous.

```csharp
var display = "Breakfast time";
var localizedDisplay =
  NSBundle.MainBundle.LocalizedString (display, comment:"greeting");
displayText.SetText (localizedDisplay);
```

#### <a name="images-in-code"></a>Images dans le Code

Les images qui sont remplies par le code peuvent être définies de deux manières.

1. Vous pouvez modifier un `Image` contrôle en définissant sa valeur sur le nom de chaîne d’une image qui existe déjà dans l’application Watch, par exemple

    ```csharp
    displayImage.SetImage("gradient"); // image in Watch App (as shown above)
    ```

2. Vous pouvez déplacer une image à partir de l’extension pour le suivi à l’aide `FromBundle` et l’application choisit automatiquement l’image correcte pour la sélection de la langue de l’utilisateur. Dans l’exemple de solution, il existe une image **language@2x.png** dans chaque langue dossier et il est affiché sur `DetailController` utilisant le code suivant :

    ```csharp
    using (var image = UIImage.FromBundle ("language")) {
        displayImage.SetImage (image);
    }
    ```

    Notez que vous n’avez pas besoin de spécifier **@2x** le en faisant référence au nom de fichier de l’image.

La seconde méthode est également applicable si vous téléchargez une image à partir d’un serveur distant à restituer sur la surveillance ; Toutefois dans ce cas vous devez vous assurer que l’image que vous téléchargez est correctement localisé en fonction des préférences de l’utilisateur.

## <a name="localization"></a>Localisation

Une fois que vous avez configuré votre solution, traducteurs devrez traiter votre **.strings** fichiers et des images pour chaque langue que vous souhaitez prendre en charge.

Vous pouvez créer autant **.lproj** répertoires en tant que vous avez besoin (une pour chaque langue prise en charge). Ils sont nommés à l’aide des codes de langue, tel que **en**, **es**, **dé**, **ja**, **pt-BR**, etc. (pour l’anglais Espagnol, allemand, japonais et portugais (Brésil) respectivement).

L’exemple joint utilise des traductions (-générées par l’ordinateur) pour montrer comment localiser une application watchOS.

### <a name="watch-app"></a>Application Watch

Ces valeurs sont utilisées pour traduire l’interface utilisateur définie dans la table de montage séquentiel de l’application watch. Le *clé* valeur est une combinaison de chaque contrôle de table de montage séquentiel **ID de localisation** et la propriété en cours de traduction.

Il est recommandé d’ajouter des commentaires qui contient le texte d’origine dans le fichier afin que les traducteurs pour savoir ce que la traduction.

#### <a name="eslprojinterfacestrings"></a>es.lproj/Interface.strings

Les chaînes de langue Espagnol (traduits) pour la table de montage séquentiel sont présentées ci-dessous. Il est utile d’ajouter des commentaires à chaque ligne, car il est difficile de savoir ce que le **ID de localisation** fait référence à sinon :

```csharp
"AgC-eL-Hgc.title" = "Spanish"; // app screen heading
"0.text" = "Bienvenido a WatchL10n"; // Welcome to WatchL10n
"1.text" = "Ajustes de idioma están en Apple Watch App"; // Change the language in the Apple Watch App
"2.title" = "Saludos"; // Greetings
"6.title" = "2nd"; // second screen heading
"39.text" = "Segunda pantalla"; // second screen
```

### <a name="watch-extension"></a>Extension Watch

Ces valeurs sont utilisées dans le code pour convertir les informations avant d’être affichées à l’utilisateur. Le *clé* est sélectionné par le développeur, tandis que leur écriture de code et contient généralement la chaîne réelle à traduire.

#### <a name="eslprojlocalizablestrings-file"></a>fichier de es.lproj/Localizable.strings

Les chaînes de langage Spansish (traduits) :

```csharp
"Breakfast time" = "la hora del desayuno"; // morning
"Lunch time" = "hora de comer"; // midday
"Dinner time" = "hora de la cena"; // evening
"Bed time" = "la hora de dormir"; // night
```

## <a name="testing"></a>Test

La méthode pour modifier les préférences de langue diffère entre le simulateur et les périphériques physiques.

### <a name="simulator"></a>Simulateur

Sur le simulateur, sélectionnez la langue à tester à l’aide de l’iOS **paramètres** application (l’icône grise gears dans l’écran d’accueil de simulateur).

  ![](localization-images/sim-settings-sml.png "Les paramètres de localisation d’application iOS")

### <a name="watch-device"></a>Dispositif de suivi

Lors du test avec un espion, modifier la langue de la surveillance dans le **Apple Watch** application sur l’iPhone apparié.

  ![](localization-images/phone-settings-sml.png "Modifier la langue de l’observation dans l’application Apple Watch sur l’iPhone apparié")



## <a name="related-links"></a>Liens associés

- [WatchLocalization (exemple)](https://developer.xamarin.com//samples/monotouch/watchOS/WatchLocalization/)
