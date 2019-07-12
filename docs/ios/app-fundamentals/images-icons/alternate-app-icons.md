---
title: Autres icônes d’applications dans Xamarin.iOS
description: Ce document décrit comment utiliser les autres icônes d’applications dans Xamarin.iOS. Il explique comment ajouter ces icônes à un projet Xamarin.iOS, comment modifier le fichier Info.plist et comment gérer icône de l’application par programme.
ms.prod: xamarin
ms.assetid: 302fa818-33b9-4ea1-ab63-0b2cb312299a
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/29/2017
ms.openlocfilehash: fc4ef6eb16893cc1df0d980d27af8589a280e933
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832424"
---
# <a name="alternate-app-icons-in-xamarinios"></a>Autres icônes d’applications dans Xamarin.iOS

_Cet article couvre à l’aide d’autres icônes d’applications dans Xamarin.iOS._

Apple a ajouté plusieurs améliorations à iOS 10.3 qui permettent à une application gérer son icône :

- `ApplicationIconBadgeNumber` -Obtient ou définit le badge de l’icône d’application Springboard.
- `SupportsAlternateIcons` -If `true` l’application a un autre ensemble d’icônes.
- `AlternateIconName` -Retourne le nom de l’icône de remplacement actuellement sélectionné ou `null` si vous utilisez l’icône principale.
- `SetAlternameIconName` -Utilisez cette méthode pour basculer d’icône de l’application vers l’icône de remplacement donné.

![](alternate-app-icons-images/icons04.png "Un exemple d’alerte lorsqu’une application modifie son icône")

<a name="Adding-Alternate-Icons" />

## <a name="adding-alternate-icons-to-a-xamarinios-project"></a>Ajout d’icônes d’autre à un projet Xamarin.iOS

Pour permettre à une application de passer à une autre icône, une collection d’images d’icônes devrez être inclus dans le projet d’application Xamarin.iOS. Ces images ne peut pas être ajoutés au projet à l’aide de la manière classique `Assets.xcassets` (méthode), ils doivent être ajoutés à la **ressources** dossier directement.

Effectuez ce qui suit :

1. Sélectionnez les images d’icône requis dans un dossier, sélectionner tout et faites-les glisser jusqu'à la **ressources** dossier dans le **l’Explorateur de solutions**:

    ![](alternate-app-icons-images/icons00.png "Sélectionnez les images d’icônes dans un dossier")

2. Lorsque vous y êtes invité, sélectionnez **copie**, **utiliser la même action pour tous les fichiers sélectionnés** et cliquez sur le **OK** bouton :

    ![](alternate-app-icons-images/icons02.png "Le fichier à ajouter à la boîte de dialogue dossier")

3. Le **ressources** dossier doit ressembler à ce qui suit lors de la fin :

    ![](alternate-app-icons-images/icons01.png "Le dossier de ressources doit ressembler à ceci")

<a name="Modifying-the-Info.plist-File" />

## <a name="modifying-the-infoplist-file"></a>Modification du fichier Info.plist

Avec les images nécessaires ajoutés à la **ressources** dossier, le [CFBundleAlternateIcons](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/CoreFoundationKeys.html#//apple_ref/doc/uid/TP40009249-SW13) clé devra être ajouté pour le projet **Info.plist** fichier. Cette clé définira le nom de l’icône Nouveau et les images qui la composent.

Effectuez ce qui suit :

1. Dans l’**Explorateur de solutions**, double-cliquez sur le fichier **Info.plist** pour l’ouvrir et le modifier.
2. Basculez vers le **Source** vue.
3. Ajouter un **icônes du Bundle** de clé et de laisser le **Type** définie sur **dictionnaire**.
4. Ajouter un `CFBundleAlternateIcons` de clé et définissez la **Type** à **dictionnaire**.
5. Ajouter un `AppIcon2` de clé et définissez la **Type** à **dictionnaire**. Ce sera le nom du nouveau jeu d’icône autre application.
6. Ajouter un `CFBundleIconFiles` de clé et définissez la **Type** à **tableau**
7. Ajouter une nouvelle chaîne à la `CFBundleIconFiles` tableau pour chaque fichier d’icône en omettant l’extension et la `@2x`, `@3x`, les suffixes etc. (exemple `100_icon`). Répétez cette étape pour chaque fichier qui compose l’ensemble de l’icône de remplacement.
8. Ajouter un `UIPrerenderedIcon` clés pour le `AppIcon2` dictionnaire, définissez le **Type** à **booléenne** et la valeur à **non**.
9. Enregistrez les modifications dans le fichier.

Résultant **Info.plist** fichier doit ressembler à ce qui suit lors de la fin :

![](alternate-app-icons-images/icons03.png "Le fichier Info.plist complet")

Ou, comme cela si ouvert dans un éditeur de texte :

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

## <a name="managing-the-apps-icon"></a>Icône de l’application de la gestion des 

Avec les images d’icônes inclus dans le projet Xamarin.iOS et **Info.plist** fichier correctement configuré, le développeur peut utiliser une des nombreuses nouvelles fonctionnalités ajoutées à iOS 10.3 pour contrôler l’icône de l’application.

Le `SupportsAlternateIcons` propriété de la `UIApplication` classe permet au développeur de voir si une application prend en charge les autres icônes. Par exemple :

```csharp
// Can the app select a different icon?
PrimaryIconButton.Enabled = UIApplication.SharedApplication.SupportsAlternateIcons;
AlternateIconButton.Enabled = UIApplication.SharedApplication.SupportsAlternateIcons;
```

Le `ApplicationIconBadgeNumber` propriété de la `UIApplication` classe permet au développeur obtenir ou définir le nombre de badge actuelle de l’icône d’application Springboard. La valeur par défaut est zéro (0). Par exemple :

```csharp
// Set the badge number to 1
UIApplication.SharedApplication.ApplicationIconBadgeNumber = 1;
```

Le `AlternateIconName` propriété de la `UIApplication` classe permet au développeur d’obtenir le nom de l’icône d’application autre actuellement sélectionné ou la valeur `null` si l’application est à l’aide de l’icône principale. Par exemple :

```csharp
// Get the name of the currently selected alternate
// icon set
var name = UIApplication.SharedApplication.AlternateIconName;

if (name != null ) {
    // Do something with the name
}
```

Le `SetAlternameIconName` propriété de la `UIApplication` classe permet au développeur de modifier l’icône d’application. Passez le nom de l’icône pour sélectionner ou `null` pour revenir à l’icône principale. Par exemple :

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

Lorsque l’application est exécutée et l’utilisateur sélectionner une icône de remplacement, une alerte similaire à celle-ci s’affiche :

![](alternate-app-icons-images/icons04.png "Un exemple d’alerte lorsqu’une application modifie son icône")

Si l’utilisateur bascule vers l’icône principale, une alerte similaire à celle-ci s’affiche :

![](alternate-app-icons-images/icons05.png "Un exemple d’alerte lorsqu’une application passe à l’icône principale")

<a name="Summary" />

## <a name="summary"></a>Récapitulatif

Cet article a présenté l’ajout d’autres icônes d’applications à un projet Xamarin.iOS et leur utilisation à l’intérieur de l’application.



## <a name="related-links"></a>Liens associés

- [iOSTenThree exemple](https://developer.xamarin.com/samples/ios/iOS10/iOSTenThree)
