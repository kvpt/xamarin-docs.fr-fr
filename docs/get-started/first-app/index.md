---
title: Générer votre première application Xamarin.Forms
description: Guide vidéo montrant comment générer votre première application Xamarin.Forms dans Visual Studio.
zone_pivot_groups: platform-dev16
ms.prod: xamarin
ms.assetid: 72B6AF82-4D98-47E5-AB54-0A35B3253468
ms.technology: xamarin-forms
ms.custom: video
author: conceptdev
ms.author: crdun
ms.date: 05/23/2019
ms.openlocfilehash: 245b41eea556ef36c81b337b57ce58d922e4e8fd
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68653679"
---
# <a name="build-your-first-xamarinforms-app"></a>Générer votre première application Xamarin.Forms

_Regardez cette vidéo et suivez la procédure indiquée pour créer votre première application mobile avec Xamarin.Forms._

::: zone pivot="windows"

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Build-Your-First-Android-App-with-Visual-Studio-2019-and-Xamarin/player]

## <a name="step-by-step-instructions-for-windows"></a>Instructions pas à pas pour Windows

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-firstapp/)

Suivez ces étapes, ainsi que la vidéo ci-dessus :

1. Choisissez **fichier > nouveau > projet...** ou appuyez sur le bouton **créer un nouveau projet...** :

    [![Créer un nouveau projet](images/win-2019/01-sml.png)](images/win-2019/01.png#lightbox)

2. Recherchez «Xamarin» ou choisissez **mobile** dans le menu **type de projet** . Sélectionnez le type de projet **application mobile (Xamarin. Forms)** :

    [![Filtrer les projets Xamarin](images/win-2019/02-sml.png)](images/win-2019/02.png#lightbox)

3. Choisir un nom &ndash; de projet l’exemple utilise «AwesomeApp»:

    [![Choisir un nom de projet](images/win-2019/03-sml.png)](images/win-2019/03.png#lightbox)

4. Cliquez sur le type de projet **vide** et vérifiez que **Android** et **iOS** sont sélectionnés:

    [![Android et iOS, avec .NET Standard](images/win-2019/04-sml.png)](images/win-2019/04.png#lightbox)

5. Attendez que les paquets NuGet soient restaurés (un message indiquant la fin de la restauration s’affiche dans la barre d’état).

6. Lancez l’émulateur Android en appuyant sur le bouton de débogage (ou sur l’élément de menu **Déboguer > Démarrer le débogage**).

7. Modifiez **MainPage.xaml** en ajoutant ce code XAML avant la fin de `</StackLayout>` :

    ```xaml
    <Button Text="Click Me" Clicked="Button_Clicked" />
    ```

8. Modifiez **MainPage.xaml.cs** en ajoutant ce code à la fin de la classe :

    ```csharp
    int count = 0;
    void Button_Clicked(object sender, System.EventArgs e)
    {
        count++;
        ((Button)sender).Text = $"You clicked {count} times.";
    }
    ```

9. Déboguez l’application sur Android :

    ![Application Android](images/win/07-sml.png)

    > [!TIP]
    > Il est possible de générer et déboguer l’application iOS à partir de Visual Studio avec un ordinateur Mac en réseau. Pour plus d’informations, consultez les [instructions de configuration](~/ios/get-started/installation/windows/index.md).

## <a name="build-an-ios-app-in-visual-studio-2019"></a>Générer une application iOS dans Visual Studio 2019

Cette vidéo décrit le processus de création et de test d’une application iOS à l’aide de Visual Studio 2019 sur Windows:

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Build-Your-First-iOS-App-with-Visual-Studio-2019-and-Xamarin/player]

::: zone-end
::: zone pivot="win-vs2017"

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Building-Your-First-Android--iOS-App-in-Visual-Studio-2017/player]

## <a name="step-by-step-instructions-for-windows"></a>Instructions pas à pas pour Windows

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-firstapp/)

Suivez ces étapes, ainsi que la vidéo ci-dessus :

1. Choisissez **Fichier > Nouveau > Projet**, ou appuyez sur le bouton **Créer un projet**, puis sélectionnez **Visual C# > Multiplateforme > Application mobile (Xamarin.Forms)**  :

    [![Application mobile (Xamarin.Forms)](images/win/01-sml.png)](images/win/01.png#lightbox)

2. Vérifiez qu’**Android** et **iOS** sont sélectionnés, ainsi que le partage de code **.NET Standard** :

    [![Android et iOS, avec .NET Standard](images/win/02-sml.png)](images/win/02.png#lightbox)

3. Attendez que les paquets NuGet soient restaurés (un message indiquant la fin de la restauration s’affiche dans la barre d’état).

4. Lancez l’émulateur Android en appuyant sur le bouton de débogage (ou sur l’élément de menu **Déboguer > Démarrer le débogage**).

5. Modifiez **MainPage.xaml** en ajoutant ce code XAML avant la fin de `</StackLayout>` :

    ```xaml
    <Button Text="Click Me" Clicked="Button_Clicked" />
    ```

6. Modifiez **MainPage.xaml.cs** en ajoutant ce code à la fin de la classe :

    ```csharp
    int count = 0;
    void Button_Clicked(object sender, System.EventArgs e)
    {
        count++;
        ((Button)sender).Text = $"You clicked {count} times.";
    }
    ```

7. Déboguez l’application sur Android :

    ![Application Android](images/win/07-sml.png)

    > [!TIP]
    > Il est possible de générer et déboguer l’application iOS à partir de Visual Studio avec un ordinateur Mac en réseau. Pour plus d’informations, consultez les [instructions de configuration](~/ios/get-started/installation/windows/index.md).

::: zone-end
::: zone pivot="macos"

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Building-Your-First-iOS--Android-App-in-Visual-Studio-for-Mac/player]

## <a name="step-by-step-instructions-for-mac"></a>Instructions pas à pas pour Mac

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-firstapp/)

Suivez ces étapes, ainsi que la vidéo ci-dessus :

1. Choisissez **Fichier > Nouvelle solution**, ou appuyez sur le bouton **Nouveau projet**, puis sélectionnez **Multiplateforme > Application > Application Forms vide** :

    [![Application Forms vide](images/01-sml.png)](images/01.png#lightbox)

2. Vérifiez qu’**Android** et **iOS** sont sélectionnés, ainsi que le partage de code **.NET Standard** :

    [![Android et iOS, avec .NET Standard](images/02-sml.png)](images/02.png#lightbox)

3. Restaurez les paquets NuGet en cliquant avec le bouton droit sur la solution :

    ![Application Android](images/03-sml.png)

4. Lancez l’émulateur Android en appuyant sur le bouton de débogage (ou sur **Exécuter > Démarrer le débogage**).

5. Modifiez **MainPage.xaml** en ajoutant ce code XAML avant la fin de `</StackLayout>` :

    ```xaml
    <Button Text="Click Me" Clicked="Handle_Clicked" />
    ```

6. Modifiez **MainPage.xaml.cs** en ajoutant ce code à la fin de la classe :

    ```csharp
    int count = 0;
    void Handle_Clicked(object sender, System.EventArgs e)
    {
        count++;
        ((Button)sender).Text = $"You clicked {count} times.";
    }
    ```

7. Déboguez l’application sur Android :

    ![Application Android](images/07-sml.png)

8. Cliquez avec le bouton droit pour définir le **projet de démarrage** à iOS :

    [![Définir le projet de démarrage à iOS](images/08-sml.png)](images/08.png#lightbox)

9. Déboguez l’application sur iOS :

    ![Application iOS](images/09-sml.png)

::: zone-end

Vous pouvez télécharger le code complet à partir de la [galerie d’exemples](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-firstapp/) ou le voir sur [GitHub](https://github.com/xamarin/xamarin-forms-samples/tree/master/GetStarted/FirstApp).

## <a name="next-steps"></a>Étapes suivantes

- [Démarrage rapide d’une seule page](~/get-started/quickstarts/single-page.md) &ndash; Générez une application plus fonctionnelle.
- [Exemples Xamarin.Forms](~/xamarin-forms/samples/index.yml) &ndash; Téléchargez et exécutez des exemples de code et des exemples d’applications.
- [Livre électronique sur la création d’applications mobiles](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md) &ndash; Chapitres détaillés sur le développement de Xamarin.Forms, disponibles au format PDF et comprenant des centaines d’exemples supplémentaires.
