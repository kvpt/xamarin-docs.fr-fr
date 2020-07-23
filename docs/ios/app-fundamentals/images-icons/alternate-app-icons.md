---
title: Autres icônes d’application dans Xamarin. iOS
description: Ce document explique comment utiliser d’autres icônes d’application dans Xamarin. iOS. Il explique comment ajouter ces icônes à un projet Xamarin. iOS, comment modifier le fichier info. plist et comment gérer l’icône de l’application par programme.
ms.prod: xamarin
ms.assetid: 302fa818-33b9-4ea1-ab63-0b2cb312299a
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/29/2017
ms.openlocfilehash: 39a18a775946c2f139b4c032d2c360bc5680a0e7
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86937915"
---
# <a name="alternate-app-icons-in-xamarinios"></a>Autres icônes d’application dans Xamarin. iOS

_Cet article traite de l’utilisation d’autres icônes d’application dans Xamarin. iOS._

Apple a ajouté plusieurs améliorations à iOS 10,3 qui permettent à une application de gérer son icône :

- `ApplicationIconBadgeNumber`-Obtient ou définit le badge de l’icône d’application dans le Springboard.
- `SupportsAlternateIcons`-Si `true` l’application possède un autre ensemble d’icônes.
- `AlternateIconName`-Retourne le nom de l’autre icône actuellement sélectionnée ou `null` si vous utilisez l’icône principale.
- `SetAlternameIconName`-Utilisez cette méthode pour faire passer l’icône de l’application à l’autre icône donnée.

![Exemple d’alerte quand une application modifie son icône](alternate-app-icons-images/icons04.png)

<a name="Adding-Alternate-Icons"></a>

## <a name="adding-alternate-icons-to-a-xamarinios-project"></a>Ajout d’autres icônes à un projet Xamarin. iOS

Pour permettre à une application de basculer vers une autre icône, une collection d’images d’icône doit être incluse dans le projet d’application Xamarin. iOS. Ces images ne peuvent pas être ajoutées au projet à l’aide de la `Assets.xcassets` méthode classique, elles doivent être ajoutées directement au dossier de **ressources** .

Effectuez les actions suivantes :

1. Sélectionnez les images d’icône requises dans un dossier, sélectionnez tout et faites-les glisser vers le dossier **Resources** dans le **Explorateur de solutions**:

    ![Sélectionner les images d’icônes dans un dossier](alternate-app-icons-images/icons00.png)

2. Lorsque vous y êtes invité, sélectionnez **copier**, **Utilisez la même action pour tous les fichiers sélectionnés** , puis cliquez sur le bouton **OK** :

    ![Boîte de dialogue Ajouter un fichier à un dossier](alternate-app-icons-images/icons02.png)

3. Lorsque vous avez terminé, le dossier **ressources** doit se présenter comme suit :

    ![Le dossier Resources doit ressembler à ceci](alternate-app-icons-images/icons01.png)

<a name="Modifying-the-Info.plist-File"></a>

## <a name="modifying-the-infoplist-file"></a>Modification du fichier info. plist

Une fois les images requises ajoutées au dossier **Resources** , la clé [CFBundleAlternateIcons](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/CoreFoundationKeys.html#//apple_ref/doc/uid/TP40009249-SW13) doit être ajoutée au fichier **info. plist** du projet. Cette clé définit le nom de la nouvelle icône et les images qui la composent.

Effectuez les actions suivantes :

1. Dans l’**Explorateur de solutions**, double-cliquez sur le fichier **Info.plist** pour l’ouvrir et le modifier.
2. Basculez sur la vue **Source**.
3. Ajoutez une clé d' **icônes de regroupement** et laissez le **type** défini sur **dictionary**.
4. Ajoutez une `CFBundleAlternateIcons` clé et définissez le **type** sur **dictionary**.
5. Ajoutez une `AppIcon2` clé et définissez le **type** sur **dictionary**. Il s’agit du nom du nouveau jeu d’icônes d’application de remplacement.
6. Ajouter une `CFBundleIconFiles` clé et définir le **type** sur **Array**
7. Ajoutez une nouvelle chaîne au `CFBundleIconFiles` tableau pour chaque fichier d’icône en ignorant l’extension et les `@2x` `@3x` suffixes,, etc. (exemple `100_icon` ). Répétez cette étape pour chaque fichier qui compose le jeu d’icônes de remplacement.
8. Ajoutez une `UIPrerenderedIcon` clé au `AppIcon2` dictionnaire, définissez le **type** sur **booléen** et la valeur sur **non**.
9. Enregistrez les modifications du fichier.

Une fois terminé, le fichier **info. plist** doit se présenter comme suit :

![Fichier info. plist terminé](alternate-app-icons-images/icons03.png)

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

<a name="Managing-the-Apps-Icon"></a>

## <a name="managing-the-apps-icon"></a>Gestion de l’icône de l’application 

Avec les images d’icône incluses dans le projet Xamarin. iOS et le fichier **info. plist** correctement configuré, le développeur peut utiliser l’une des nombreuses nouvelles fonctionnalités ajoutées à IOS 10,3 pour contrôler l’icône de l’application.

La `SupportsAlternateIcons` propriété de la `UIApplication` classe permet au développeur de voir si une application prend en charge d’autres icônes. Par exemple :

```csharp
// Can the app select a different icon?
PrimaryIconButton.Enabled = UIApplication.SharedApplication.SupportsAlternateIcons;
AlternateIconButton.Enabled = UIApplication.SharedApplication.SupportsAlternateIcons;
```

La `ApplicationIconBadgeNumber` propriété de la `UIApplication` classe permet au développeur d’accéder ou de définir le numéro de badge actuel de l’icône d’application dans Springboard. La valeur par défaut est zéro (0). Par exemple :

```csharp
// Set the badge number to 1
UIApplication.SharedApplication.ApplicationIconBadgeNumber = 1;
```

La `AlternateIconName` propriété de la `UIApplication` classe permet au développeur d’afficher le nom de l’icône d’application alternative actuellement sélectionnée ou retourne `null` si l’application utilise l’icône principale. Par exemple :

```csharp
// Get the name of the currently selected alternate
// icon set
var name = UIApplication.SharedApplication.AlternateIconName;

if (name != null ) {
    // Do something with the name
}
```

La `SetAlternameIconName` propriété de la `UIApplication` classe permet au développeur de modifier l’icône de l’application. Transmettez le nom de l’icône pour sélectionner ou `null` pour revenir à l’icône principale. Par exemple :

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

![Exemple d’alerte quand une application modifie son icône](alternate-app-icons-images/icons04.png)

Si l’utilisateur revient à l’icône principale, une alerte semblable à la suivante s’affiche :

![Exemple d’alerte quand une application passe à l’icône principale](alternate-app-icons-images/icons05.png)

<a name="Summary"></a>

## <a name="summary"></a>Résumé

Cet article a abordé l’ajout d’icônes d’application alternatives à un projet Xamarin. iOS et son utilisation dans l’application.

## <a name="related-links"></a>Liens associés

- [Exemple iOSTenThree](https://docs.microsoft.com/samples/xamarin/ios-samples/ios10-iostenthree/)
