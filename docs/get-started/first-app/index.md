---
titre : « créer votre première application Xamarin.Forms » Description : « Guide vidéo qui montre comment créer votre première Xamarin.Forms application dans Visual Studio ».
zone_pivot_groups : Platform-dev16 ms. Prod : xamarin ms. AssetID : 72B6AF82-4D98-47E5-AB54-0A35B3253468 ms. Technology : xamarin-Forms ms. Custom : Video Author : conceptdev ms. Author : crdun ms. Date : 05/23/2019 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---
# <a name="build-your-first-xamarinforms-app"></a>Créer votre première Xamarin.Forms application

_Regardez cette vidéo et suivez la procédure pour créer votre première application mobile avec Xamarin.Forms ._

::: zone pivot="windows"

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Build-Your-First-Android-App-with-Visual-Studio-2019-and-Xamarin/player]

## <a name="step-by-step-instructions-for-windows"></a>Instructions pas à pas pour Windows

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-firstapp/)

Suivez ces étapes, ainsi que la vidéo ci-dessus :

1. Choisissez **Fichier > Nouveau > Projet**, ou appuyez sur le bouton **Créer un projet** :

    [![Création d'un projet](images/win-2019/01-sml.png)](images/win-2019/01.png#lightbox)

2. Recherchez « Xamarin », ou choisissez **Mobile** dans le menu **Type de projet**. Sélectionnez le type de projet **application mobile ( Xamarin.Forms )** :

    [![Filtrer les projets Xamarin](images/win-2019/02-sml.png)](images/win-2019/02.png#lightbox)

3. Choisissez un nom de projet. L’exemple utilise « AwesomeApp » :

    [![Choisir un nom de projet](images/win-2019/03-sml.png)](images/win-2019/03.png#lightbox)

4. Cliquez sur le type de projet **Vide**, puis vérifiez qu’**Android** et **iOS** sont sélectionnés :

    [![Android et iOS, avec .NET Standard](images/win-2019/04-sml.png)](images/win-2019/04.png#lightbox)

5. Attendez que les paquets NuGet soient restaurés (un message indiquant la fin de la restauration s’affiche dans la barre d’état).

6. Les nouvelles installations de Visual Studio 2019 ne disposent pas d’un Émulateur Android configuré. Cliquez sur la flèche déroulante vers le bas du bouton **Déboguer**, puis choisissez **Créer un émulateur Android** pour lancer l’écran de création de l’émulateur :

    ![Liste déroulante Créer un émulateur Android](images/win-2019/debug-dropdown.png)

7. Dans l’écran de création de l’émulateur, utilisez les paramètres par défaut, puis cliquez sur le bouton **Créer** :

    [![Écran de création de l’émulateur Android](images/win-2019/create-emulator-sml.png)](images/win-2019/create-emulator.png#lightbox)

8. La création d’un émulateur vous ramène à la fenêtre du Gestionnaire d’appareils. Cliquez sur le bouton **Démarrer** pour lancer le nouvel émulateur :

    ![Émulateur Android dans le Gestionnaire d’appareils](images/win-2019/start-emulator.png)

9. Visual Studio 2019 doit maintenant afficher le nom du nouvel émulateur sur le bouton **Déboguer** :

    ![Nom de l’Émulateur Android sur le bouton Déboguer](images/win-2019/debug-emulator-name.png)

10. Cliquez sur le bouton **Déboguer** pour générer et déployer l’application sur l’Émulateur Android :

    ![Émulateur Android affichant l’application](images/win-2019/android-emulator.png)

## <a name="customize-the-application"></a>Personnaliser l’application

Vous pouvez personnaliser l’application pour ajouter des fonctionnalités interactives. Effectuez les étapes suivantes pour ajouter à l’application une fonctionnalité d’interaction avec l’utilisateur :

1. Modifiez **MainPage.xaml** en ajoutant ce code XAML avant la fin de `</StackLayout>` :

    ```xaml
    <Button Text="Click Me" Clicked="Button_Clicked" />
    ```

2. Modifiez **MainPage.xaml.cs** en ajoutant ce code à la fin de la classe :

    ```csharp
    int count = 0;
    void Button_Clicked(object sender, System.EventArgs e)
    {
        count++;
        ((Button)sender).Text = $"You clicked {count} times.";
    }
    ```

3. Déboguez l’application sur Android :

    ![Application Android](images/win/07-sml.png)

> [!NOTE]
> L’exemple d’application comprend la fonctionnalité interactive supplémentaire qui n’est pas abordée dans la vidéo.

## <a name="build-an-ios-app-in-visual-studio-2019"></a>Générer une application iOS dans Visual Studio 2019

Vous pouvez générer et déboguer l’application iOS à partir de Visual Studio à l’aide d’un ordinateur Mac en réseau. Pour plus d’informations, consultez les [instructions de configuration](~/ios/get-started/installation/windows/index.md).

Cette vidéo décrit le processus de génération et de test d’une application iOS à l’aide de Visual Studio 2019 sur Windows :

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Build-Your-First-iOS-App-with-Visual-Studio-2019-and-Xamarin/player]

::: zone-end
::: zone pivot="win-vs2017"

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Building-Your-First-Android--iOS-App-in-Visual-Studio-2017/player]

## <a name="step-by-step-instructions-for-windows"></a>Instructions pas à pas pour Windows

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-firstapp/)

Suivez ces étapes, ainsi que la vidéo ci-dessus :

1. Choisissez **fichier > nouveau > projet...** ou appuyez sur le bouton **créer un nouveau projet.** .., puis sélectionnez **Visual C# > multiplateforme > application mobile ( Xamarin.Forms )**:

    [![Application mobile ( Xamarin.Forms )](images/win/01-sml.png)](images/win/01.png#lightbox)

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

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-firstapp/)

Suivez ces étapes, ainsi que la vidéo ci-dessus :

1. Choisissez **Fichier > Nouvelle solution**, ou appuyez sur le bouton **Nouveau projet**, puis sélectionnez **Multiplateforme > Application > Application Forms vide** :

    [![Application Formulaires vide](images/01-sml.png)](images/01.png#lightbox)

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

    [![Définir le projet de démarrage sur iOS](images/08-sml.png)](images/08.png#lightbox)

9. Déboguez l’application sur iOS :

    ![Application iOS](images/09-sml.png)

::: zone-end

Vous pouvez télécharger le code complet à partir de la [galerie d’exemples](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-firstapp/) ou le voir sur [GitHub](https://github.com/xamarin/xamarin-forms-samples/tree/master/GetStarted/FirstApp).

## <a name="next-steps"></a>Étapes suivantes

- [Démarrage rapide : application monopage](~/get-started/quickstarts/single-page.md) &ndash; Générer une application plus opérationnelle.
- [ Xamarin.Forms Exemples](~/xamarin-forms/samples/index.md) &ndash; Téléchargez et exécutez des exemples de code et des exemples d’applications.
- [Création d’Mobile Apps livre électronique](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md) &ndash; Chapitres détaillés qui enseignent Xamarin.Forms le développement, disponibles au format PDF et qui incluent des centaines d’exemples supplémentaires.
