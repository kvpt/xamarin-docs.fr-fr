---
title: Autres icônes d’application dans Xamarin. iOS
description: Ce document explique comment utiliser d’autres icônes d’application dans Xamarin. iOS. Il explique comment ajouter ces icônes à un projet Xamarin. iOS, comment modifier le fichier info. plist et comment gérer l’icône de l’application par programme.
ms.prod: xamarin
ms.assetid: 302fa818-33b9-4ea1-ab63-0b2cb312299a
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/29/2017
ms.openlocfilehash: ed31f1dca3f823ccd0374b4fcbac1bbd9e80e022
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73004318"
---
# <a name="alternate-app-icons-in-xamarinios"></a>Autres icônes d’application dans Xamarin. iOS

_Cet article traite de l’utilisation d’autres icônes d’application dans Xamarin. iOS._

Apple a ajouté plusieurs améliorations à iOS 10,3 qui permettent à une application de gérer son icône :

- `ApplicationIconBadgeNumber` : Obtient ou définit le badge de l’icône de l’application dans le Springboard.
- `SupportsAlternateIcons`-si `true` l’application possède un autre ensemble d’icônes.
- `AlternateIconName` : retourne le nom de l’autre icône actuellement sélectionnée ou `null` si l’icône principale est utilisée.
- `SetAlternameIconName` : utilisez cette méthode pour faire passer l’icône de l’application à l’autre icône donnée.

![](alternate-app-icons-images/icons04.png "A sample alert when an app changes its icon")

<a name="Adding-Alternate-Icons" />

## <a name="adding-alternate-icons-to-a-xamarinios-project"></a>Ajout d’autres icônes à un projet Xamarin. iOS

Pour permettre à une application de basculer vers une autre icône, une collection d’images d’icône doit être incluse dans le projet d’application Xamarin. iOS. Ces images ne peuvent pas être ajoutées au projet à l’aide de la méthode `Assets.xcassets` classique, elles doivent être ajoutées directement au dossier **ressources** .

Effectuez ce qui suit :

1. Sélectionnez les images d’icône requises dans un dossier, sélectionnez tout et faites-les glisser vers le dossier **Resources** dans le **Explorateur de solutions**:

    ![](alternate-app-icons-images/icons00.png "Select the icons images from a folder")

2. Lorsque vous y êtes invité, sélectionnez **copier**, **Utilisez la même action pour tous les fichiers sélectionnés** , puis cliquez sur le bouton **OK** :

    ![](alternate-app-icons-images/icons02.png "The Add File to Folder dialog box")

3. Lorsque vous avez terminé, le dossier **ressources** doit se présenter comme suit :

    ![](alternate-app-icons-images/icons01.png "The Resources folder should look like this")

<a name="Modifying-the-Info.plist-File" />

## <a name="modifying-the-infoplist-file"></a>Modification du fichier info. plist

Une fois les images requises ajoutées au dossier **Resources** , la clé [CFBundleAlternateIcons](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/CoreFoundationKeys.html#//apple_ref/doc/uid/TP40009249-SW13) doit être ajoutée au fichier **info. plist** du projet. Cette clé définit le nom de la nouvelle icône et les images qui la composent.

Effectuez ce qui suit :

1. Dans l’**Explorateur de solutions**, double-cliquez sur le fichier **Info.plist** pour l’ouvrir et le modifier.
2. Basculez en mode **source** .
3. Ajoutez une clé d' **icônes de regroupement** et laissez le **type** défini sur **dictionary**.
4. Ajoutez une clé de `CFBundleAlternateIcons` et définissez le **type** sur **dictionary**.
5. Ajoutez une clé de `AppIcon2` et définissez le **type** sur **dictionary**. Il s’agit du nom du nouveau jeu d’icônes d’application de remplacement.
6. Ajouter une clé de `CFBundleIconFiles` et définir le **type** sur **Array**
7. Ajoutez une nouvelle chaîne au tableau de `CFBundleIconFiles` pour chaque fichier d’icône en ignorant l’extension et les suffixes `@2x`, `@3x`, etc. (exemple `100_icon`). Répétez cette étape pour chaque fichier qui compose le jeu d’icônes de remplacement.
8. Ajoutez une clé de `UIPrerenderedIcon` au dictionnaire `AppIcon2`, définissez le **type** sur **booléen** et la valeur sur **non**.
9. Enregistrez les modifications dans le fichier.

Une fois terminé, le fichier **info. plist** doit se présenter comme suit :

![](alternate-app-icons-images/icons03.png "The completed Info.plist file")

Ou comme ceci si vous l’ouvrez dans un éditeur de texte :

```xml
<key>CFBundleIcons</key>
<dict>
    <key>CFBundleAlternateIcons</key>
    <dict>
        <key>AppIcon2</key>
        <dict>
            <key>CFBundleIconFiles</key>
            <array>
                <string>100_icon</string>
                <string>114_icon</string>
                <string>120_icon</string>
                <string>144_icon</string>
                <string>152_icon</string>
                <string>167_icon</string>
                <string>180_icon</string>
                <string>29_icon</string>
                <string>40_icon</string>
                <string>50_icon</string>
                <string>512_icon</string>
                <string>57_icon</string>
                <string>58_icon</string>
                <string>72_icon</string>
                <string>76_icon</string>
                <string>80_icon</string>
                <string>87_icon</string>
            </array>
            <key>UIPrerenderedIcon</key>
            <false/>
        </dict>
    </dict>
</dict>
```

<a name="Managing-the-Apps-Icon" />

## <a name="managing-the-apps-icon"></a>Gestion de l’icône de l’application 

Avec les images d’icône incluses dans le projet Xamarin. iOS et le fichier **info. plist** correctement configuré, le développeur peut utiliser l’une des nombreuses nouvelles fonctionnalités ajoutées à IOS 10,3 pour contrôler l’icône de l’application.

La propriété `SupportsAlternateIcons` de la classe `UIApplication` permet au développeur de voir si une application prend en charge d’autres icônes. Exemple :

```csharp
// Can the app select a different icon?
PrimaryIconButton.Enabled = UIApplication.SharedApplication.SupportsAlternateIcons;
AlternateIconButton.Enabled = UIApplication.SharedApplication.SupportsAlternateIcons;
```

La propriété `ApplicationIconBadgeNumber` de la classe `UIApplication` permet au développeur d’extraire ou de définir le numéro de badge actuel de l’icône d’application dans le Springboard. La valeur par défaut est zéro (0). Exemple :

```csharp
// Set the badge number to 1
UIApplication.SharedApplication.ApplicationIconBadgeNumber = 1;
```

La propriété `AlternateIconName` de la classe `UIApplication` permet au développeur d’afficher le nom de l’icône d’application alternative actuellement sélectionnée ou retourne `null` si l’application utilise l’icône principale. Exemple :

```csharp
// Get the name of the currently selected alternate
// icon set
var name = UIApplication.SharedApplication.AlternateIconName;

if (name != null ) {
    // Do something with the name
}
```

La propriété `SetAlternameIconName` de la classe `UIApplication` permet au développeur de modifier l’icône de l’application. Transmettez le nom de l’icône à sélectionner ou `null` pour revenir à l’icône principale. Exemple :

```csharp
partial void UsePrimaryIcon (Foundation.NSObject sender)
{
    UIApplication.SharedApplication.SetAlternateIconName (null, (err) => {
        Console.WriteLine ("Set Primary Icon: {0}", err);
    });
}

partial void UseAlternateIcon (Foundation.NSObject sender)
{
    UIApplication.SharedApplication.SetAlternateIconName ("AppIcon2", (err) => {
        Console.WriteLine ("Set Alternate Icon: {0}", err);
    });
}
```

Lorsque l’application est exécutée et que l’utilisateur sélectionne une autre icône, une alerte semblable à la suivante s’affiche :

![](alternate-app-icons-images/icons04.png "A sample alert when an app changes its icon")

Si l’utilisateur revient à l’icône principale, une alerte semblable à la suivante s’affiche :

![](alternate-app-icons-images/icons05.png "A sample alert when an app changes to the primary icon")

<a name="Summary" />

## <a name="summary"></a>Récapitulatif

Cet article a abordé l’ajout d’icônes d’application alternatives à un projet Xamarin. iOS et son utilisation dans l’application.

## <a name="related-links"></a>Liens associés

- [Exemple iOSTenThree](https://docs.microsoft.com/samples/xamarin/ios-samples/ios10-iostenthree/)
