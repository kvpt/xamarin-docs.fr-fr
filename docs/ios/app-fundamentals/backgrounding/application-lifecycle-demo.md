---
title: Démonstration du cycle de vie des applications pour Xamarin. iOS
description: Ce document examine les différents événements de cycle de vie gérés par le délégué d’application dans une application iOS, en expliquant quand et comment ces événements sont gérés.
ms.prod: xamarin
ms.assetid: 5C8AACA6-49F8-4C6D-99C3-5F443C01B230
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 07/17/2018
ms.openlocfilehash: d130d28b6cf0f15dab3a743a9a3fba144b75a67d
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2019
ms.locfileid: "70289433"
---
# <a name="application-lifecycle-demo-for-xamarinios"></a>Démonstration du cycle de vie des applications pour Xamarin. iOS

Cet article et l' [exemple de code](https://docs.microsoft.com/samples/xamarin/ios-samples/lifecycledemo) illustrent les quatre États de l’application dans iOS, et le rôle des méthodes `AppDelegate` dans la notification de l’application lorsque les États sont modifiés. L’application imprime les mises à jour sur la console chaque fois que l’application change d’État :

[![](application-lifecycle-demo-images/image3-sml.png "The sample app")](application-lifecycle-demo-images/image3.png#lightbox)

[![](application-lifecycle-demo-images/image4.png "The app will print updates to the console whenever the app changes state")](application-lifecycle-demo-images/image4.png#lightbox)

## <a name="walkthrough"></a>Procédure pas à pas

1. Ouvrez le projet de **cycle de vie** dans la solution **LifecycleDemo** .
1. Ouvrez la classe `AppDelegate`. La journalisation a été ajoutée aux méthodes de cycle de vie pour indiquer quand l’application a changé d’État :

    ```csharp
    public override void OnActivated(UIApplication application)
    {
        Console.WriteLine("OnActivated called, App is active.");
    }
    public override void WillEnterForeground(UIApplication application)
    {
        Console.WriteLine("App will enter foreground");
    }
    public override void OnResignActivation(UIApplication application)
    {
        Console.WriteLine("OnResignActivation called, App moving to inactive state.");
    }
    public override void DidEnterBackground(UIApplication application)
    {
        Console.WriteLine("App entering background state.");
    }
    // not guaranteed that this will run
    public override void WillTerminate(UIApplication application)
    {
        Console.WriteLine("App is terminating.");
    }
    ```

1. Lancez l’application dans le simulateur ou sur l’appareil. `OnActivated` est appelé au lancement de l’application. L’application est maintenant à l’état _actif_ .
1. Appuyez sur le bouton d’hébergement sur le simulateur ou l’appareil pour mettre l’application en arrière-plan. `OnResignActivation` et `DidEnterBackground` seront appelés au fur et à mesure que l’application passe de `Active` à `Inactive` et à l’État `Backgrounded`. Étant donné qu’aucun code d’application n’est défini pour s’exécuter en arrière-plan, l’application est considérée comme étant _suspendue_ en mémoire.
1. Revenez à l’application pour la ramener au premier plan. `WillEnterForeground` et `OnActivated` seront tous deux appelés :

    ![](application-lifecycle-demo-images/image4.png "State changes printed to the console")

    La ligne de code suivante dans le contrôleur d’affichage est exécutée lorsque l’application est entrée au premier plan à partir de l’arrière-plan et modifie le texte affiché à l’écran :

    ```csharp
    UIApplication.Notifications.ObserveWillEnterForeground ((sender, args) => {
        label.Text = "Welcome back!";
    });
    ```

1. Appuyez sur le bouton de **démarrage** pour placer l’application en arrière-plan. Ensuite, double-cliquez sur le bouton de **démarrage** pour afficher le sélecteur d’application. Sur l’iPhone X, balayez vers le haut à partir du bas de l’écran :

    [![Le sélecteur d’application](application-lifecycle-demo-images/app-switcher-sml.png "Le sélecteur d’application")](application-lifecycle-demo-images/app-switcher.png#lightbox)
  
1. Localisez l’application dans le sélecteur d’applications et faites-la glisser vers le haut pour la supprimer (sur iOS 11, appuyez longuement jusqu’à ce que les icônes rouges s’affichent dans l’angle) :

    [![Balayer vers le haut pour supprimer une application en cours d’exécution](application-lifecycle-demo-images/app-switcher-swipe-sml.png "Balayer vers le haut pour supprimer une application en cours d’exécution")](application-lifecycle-demo-images/app-switcher-swipe.png#lightbox)

iOS met fin à l’application. Notez que `WillTerminate` n’est pas appelé, car l’application est déjà _suspendue_ en arrière-plan.

## <a name="related-links"></a>Liens connexes

- [LifecycleDemo (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/lifecycledemo)
