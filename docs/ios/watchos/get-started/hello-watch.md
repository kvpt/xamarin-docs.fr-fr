---
title: Bonjour, Watchos – procédure pas à pas
description: Ce document fournit une procédure pas à pas de création d’une application Watchos simple à l’aide de Xamarin. Il décrit comment travailler à la fois dans Visual Studio et Visual Studio pour Mac, utiliser des storyboards et répondre aux événements dans le code.
ms.prod: xamarin
ms.assetid: AD1DA488-51AB-420A-A0B7-3AE69A964A40
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 12/14/2016
ms.openlocfilehash: 3f69f10274c413a107a40b2f404b3227cfee67cf
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86936737"
---
# <a name="hello-watchos--walkthrough"></a>Bonjour, Watchos – procédure pas à pas

Après avoir créé une solution en suivant les étapes de la procédure [d’installation et d’installation](~/ios/watchos/get-started/installation.md), vous aurez 3 projets :

- L’application parent iOS utilisée pour l’installation ou d’autres tâches d’administration sur l’appareil. (Avec d’autres types d’extensions iOS, cette application est souvent appelée « conteneur ».) Avec les applications Watch, les utilisateurs peuvent commencer à exécuter l’application Watch sans **jamais** exécuter l’application parente.
- Extension Watch qui contient le code de programme de l’application Watch ; les
- L’application Watch, qui contient les ressources d’image et de Storyboard qui sont rendues sur la montre.

Vérifiez que vos [références sont correctes](~/ios/watchos/get-started/project-references.md): que l’application parente a une référence à l’extension et que l’extension a une référence à l’application Watch.

Vérifiez que les identificateurs de Bundle suivent la \* Convention. watchkitextension \* . watchkitapp et que le fichier info. plist de votre extension a la valeur **ID de Bundle WKApp** définie sur l’identificateur de Bundle de votre application Watch.

Vous devriez pouvoir exécuter votre application Watch maintenant, mais étant donné que le fichier de la table de montage séquentiel dans votre application Watch est vide, vous ne devriez pas être en mesure de le dire.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

![Explorateur de solutions](hello-watch-images/projectstructure.png)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![Explorateur de solutions](hello-watch-images/vs-projectstructure.png)

-----

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

Double-cliquez sur interface. Storyboard dans votre application Watch pour démarrer le concepteur iOS Xamarin (si vous êtes sur un Mac, vous pouvez également cliquer avec le bouton droit et **Ouvrir avec > Xcode Interface Builder**)

1. Vérifiez que les panneaux **boîte à outils** et **Propriétés** sont visibles,
1. Cliquez pour sélectionner le contrôleur d’interface.
1. Définissez l’identificateur et le titre du contrôleur d’interface sur **interfaceController** et **AIM Watch**.
1. Vérifiez que la **classe** est définie sur **InterfaceController**

    ![Définir l’identificateur et le titre du contrôleur d’interface sur interfaceController et AIM Watch](hello-watch-images/interfacecontrollerattributes.png)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Double-cliquez sur interface. Storyboard dans votre application Watch pour effectuer des modifications à l’aide du concepteur Xamarin iOS dans Visual Studio :

1. Ouvrez le volet Propriétés.
1. Remplacez la classe par **InterfaceController**;
1. Cliquez sur le contrôleur d’interface. les
1. Définissez l’identificateur et le titre du contrôleur d’interface sur **interfaceController** et **AIM Watch**.

    ![Définir l’identificateur et le titre du contrôleur d’interface sur interfaceController et AIM Watch](hello-watch-images/vs-interfacecontrollerattributes.png)

-----

Créez votre interface utilisateur :

1. À partir du panneau de la **boîte à outils** ,
1. Glissez-déplacez un **bouton** et une **étiquette** sur la scène, puis
1. Définissez le texte et les attributs des contrôles comme indiqué ci-dessous :

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

![Définir le texte et les attributs des contrôles comme indiqué](hello-watch-images/draganddrop.png)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![Définir le texte et les attributs des contrôles comme indiqué](hello-watch-images/vs-draganddrop.png)

-----

1. Définissez le **nom** dans le panneau **Propriétés** pour chaque contrôle. Dans cet exemple, nous avons utilisé `myButton` et `myLabel` .

1. Sélectionnez le bouton sur le Storyboard et accédez à la liste des **événements** du panneau **Propriétés** , puis

1. Créez une nouvelle **action** en tapant `OnButtonPress` et en appuyant sur **entrée**.
  L’action s’affiche dans la liste et une méthode partielle est créée automatiquement en C#.

![Action OnButtonPress ajoutée à un bouton](hello-watch-images/buttonaction.png)

Une fois que vous avez enregistré la table de montage séquentiel, le **InterfaceController.Designer.cs** est mis à jour avec les noms et les actions des contrôles. Si vous ouvrez ce fichier après qu’il a été mis à jour, vous pouvez voir comment le `RegisterAttribute` correspond au contrôleur et comment les contrôles d’interface utilisateur correspondent aux variables d’instance C# marquées avec le `OutletAttribute` et comment les actions sont mappées aux méthodes partielles marquées avec le `ActionAttribute` :

```csharp
// WARNING
//
// This file has been generated automatically by Visual Studio for Mac from the outlets and
// actions declared in your storyboard file.
// Manual changes to this file will not be maintained.
//
[Register ("InterfaceController")]
partial class InterfaceController
{
    [Outlet]
    [GeneratedCode ("iOS Designer", "1.0")]
    WatchKit.WKInterfaceButton myButton { get; set; }

    [Outlet]
    [GeneratedCode ("iOS Designer", "1.0")]
    WatchKit.WKInterfaceLabel myLabel { get; set; }

    [Action ("OnButtonPress:")]
    [GeneratedCode ("iOS Designer", "1.0")]
    partial void OnButtonPress (WatchKit.WKInterfaceButton sender);

    void ReleaseDesignerOutlets ()
    {
        if (myButton != null) {
            myButton.Dispose ();
            myButton = null;
        }
        if (myLabel != null) {
            myLabel.Dispose ();
            myLabel = null;
        }
    }
}
```

Maintenant, ouvrez **InterfaceController.cs** (et*non* InterfaceController.Designer.cs) et ajoutez le code suivant :

```csharp
int clickCount = 0;
partial void OnButtonPress (WatchKit.WKInterfaceButton sender)
{
  var msg = String.Format("Clicked {0} times", ++clickCount);
  myLabel.SetText(msg);
}
```

Ce code doit être assez transparent : la variable d’instance `clickCount` est incrémentée chaque fois que la fonction `OnButtonPress` est appelée. Le texte de `myLabel` est modifié pour refléter ce nombre ; `myLabel` , bien entendu, est le nom de l’une des prises que vous avez créées dans Xcode. La `partial` fonction est l’implémentation de la fonction associée au nom de l’action que vous avez spécifiée.

S’il ne s’agit pas déjà du projet de démarrage,

1. Cliquez avec le bouton droit sur votre projet d’extension Watch, puis choisissez **définir comme projet de démarrage**.

1. Définissez la cible de déploiement sur une image de simulateur compatible avec le kit espion (comme iPhone 6 iOS 8,2).

1. Appuyez sur le bouton **Déboguer** pour déclencher un lancement de build et de simulateur.

    [![Éléments de l’interface Visual Studio](hello-watch-images/readytodebug-sml.png)](hello-watch-images/readytodebug.png#lightbox)

Au lancement du simulateur, appuyez sur le bouton pour incrémenter l’étiquette.
Félicitations, vous disposez d’une application de surveillance !

![Application en cours d’exécution dans le simulateur](hello-watch-images/running.png)

## <a name="related-links"></a>Liens associés

- [Configuration et installation](~/ios/watchos/get-started/installation.md)
- [Vidéo de la première application Watch](https://blog.xamarin.com/your-first-watch-kit-app/)
