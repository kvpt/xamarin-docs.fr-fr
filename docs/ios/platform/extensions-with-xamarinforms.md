---
title: Réutiliser les pages Xamarin. Forms dans une extension iOS
description: Ce document décrit les extensions iOS et l’utilisation des pages Xamarin. Forms. Il contient une procédure pas à pas sur la création d’une extension, l’intégration de Xamarin. Forms et le rendu d’un ContentPage simple en mode natif dans un projet d’extension iOS.
ms.prod: xamarin
ms.assetid: 50076FFD-3EF6-41CC-BC7E-210DE3958F5B
ms.technology: xamarin-ios
author: alexeystrakh
ms.author: alstrakh
ms.date: 05/13/2020
ms.openlocfilehash: b9bbbc784fa9932d087e1f1c2a575eb7848d5a80
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91437114"
---
# <a name="reuse-xamarinforms-pages-in-an-ios-extension"></a>Réutiliser les pages Xamarin. Forms dans une extension iOS

les extensions iOS vous permettent de personnaliser le comportement système existant en ajoutant des fonctionnalités supplémentaires à [prédéfinies par des points d’extension iOS et MacOS](https://developer.apple.com/library/archive/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214-CH20-SW2), tels que des actions de contexte personnalisées, un remplissage automatique de mot de passe, des filtres d’appels entrants, des modificateurs de contenu de notification, etc. Xamarin. iOS prend en charge les extensions. [ce guide](./extensions.md) vous guide tout au long de la création d’une extension iOS à l’aide des outils Xamarin.

Les extensions sont distribuées dans le cadre d’une application de conteneur et activées à partir d’un point d’extension spécifique dans une application hôte. L’application de conteneur est généralement une application iOS simple, qui fournit aux utilisateurs des informations sur l’extension, comment les activer et les utiliser. Il existe trois approches principales pour partager du code entre une extension et une application de conteneur :

1. Projet iOS courant.

    Vous pouvez placer tout le code partagé entre le conteneur et l’extension dans une bibliothèque iOS partagée et faire référence à la bibliothèque à partir des deux projets. En règle générale, la bibliothèque partagée contient des UIViewControllers natifs et doit être une bibliothèque Xamarin. iOS.

1. Liens de fichier.

    Dans certains cas, l’application conteneur fournit la plupart des fonctionnalités, tandis que l’extension doit restituer une seule `UIViewController` . Avec peu de fichiers à partager, il est courant d’ajouter un lien de fichier à l’application d’extension à partir du fichier situé dans l’application conteneur.

1. Projet Xamarin. Forms commun.

    Si les pages d’application sont déjà partagées avec une autre plateforme, par exemple Android, à l’aide de l’infrastructure Xamarin. Forms, l’approche courante consiste à réimplémenter les pages requises en mode natif dans le projet d’extension, car l’extension iOS fonctionne avec les pages Native UIViewControllers et not Xamarin. Forms. Vous devez effectuer des étapes supplémentaires pour utiliser Xamarin. Forms dans l’extension iOS, qui sont expliquées ci-dessous.

## <a name="xamarinforms-in-an-ios-extension-project"></a>Xamarin. Forms dans un projet d’extension iOS

La possibilité d’utiliser Xamarin. Forms dans un projet natif est fournie via des [formulaires natifs](~/xamarin-forms/platform/native-forms.md). Elle permet `ContentPage` d’ajouter directement des pages dérivées à des projets Xamarin. iOS natifs. La `CreateViewController` méthode d’extension convertit une instance d’une page Xamarin. Forms en une page native `UIViewController` , qui peut être utilisée ou modifiée en tant que contrôleur normal. Étant donné qu’une extension iOS est un type spécial d’un projet iOS natif, vous pouvez également utiliser des **formulaires natifs** .

> [!IMPORTANT]
> Il existe de nombreuses [limitations connues](./extensions.md#limitations) pour les extensions iOS. Bien que vous puissiez utiliser Xamarin. Forms dans une extension iOS, vous devez le faire très soigneusement, en surveillant l’utilisation de la mémoire et le temps de démarrage. Dans le cas contraire, l’extension peut être arrêtée par iOS sans qu’il soit possible de la gérer correctement.

## <a name="walkthrough"></a>Procédure pas à pas

Dans cette procédure pas à pas, vous allez créer une application Xamarin. Forms, une extension Xamarin. iOS et réutiliser du code partagé dans le projet d’extension :

1. Ouvrez Visual Studio pour Mac, créez un nouveau projet Xamarin. Forms à l’aide du modèle d' **application Forms vide** , puis nommez-le **FormsShareExtension**:

    ![Créer un projet](extensions-xf-images/1.walkthrough-createproject.png)

1. Dans **FormsShareExtension/MainPage. Xaml**, remplacez le contenu par la disposition suivante :

    ```xaml
    <?xml version="1.0" encoding="utf-8" ?>
    <ContentPage
        x:Class="FormsShareExtension.MainPage"
        xmlns="http://xamarin.com/schemas/2014/forms"
        xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
        xmlns:d="http://xamarin.com/schemas/2014/forms/design"
        xmlns:local="clr-namespace:FormsShareExtension"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        x:DataType="local:MainPageViewModel"
        BackgroundColor="Orange"
        mc:Ignorable="d">
        <ContentPage.BindingContext>
            <local:MainPageViewModel Message="Hello from Xamarin.Forms!" />
        </ContentPage.BindingContext>
        <StackLayout HorizontalOptions="Center" VerticalOptions="Center">
            <Label
                Margin="20"
                Text="{Binding Message}"
                VerticalOptions="CenterAndExpand" />
            <Button Command="{Binding DoCommand}" Text="Do the job!" />
        </StackLayout>
    </ContentPage>
    ```

1. Ajoutez une nouvelle classe nommée **MainPageViewMode** au projet **FormsShareExtension** et remplacez le contenu de la classe par le code suivant :

    ```csharp
    using System;
    using System.ComponentModel;
    using System.Windows.Input;
    using Xamarin.Forms;

    namespace FormsShareExtension
    {
        public class MainPageViewModel : INotifyPropertyChanged
        {
            public event PropertyChangedEventHandler PropertyChanged;

            private string _message;
            public string Message
            {
                get { return _message; }
                set
                {
                    if (_message != value)
                    {
                        _message = value;
                        PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(nameof(Message)));
                    }
                }
            }

            private ICommand _doCommand;
            public ICommand DoCommand
            {
                get { return _doCommand; }
                set
                {
                    if(_doCommand != value)
                    {
                        _doCommand = value;
                        PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(nameof(DoCommand)));
                    }
                }
            }

            public MainPageViewModel()
            {
                DoCommand = new Command(OnDoCommandExecuted);
            }

            private void OnDoCommandExecuted(object state)
            {
                Message = $"Job {Environment.TickCount} has been completed!";
            }
        }
    }
    ```

    Le code est partagé entre toutes les plateformes et est également utilisé par une extension iOS.

1. Dans le panneau solutions, cliquez avec le bouton droit sur la solution, sélectionnez **ajouter > nouveau projet > extension de > iOS > extension d’action**, nommez-la **MyAction** et appuyez sur **créer**:

    ![Créer une extension](extensions-xf-images/2.walkthrough-createextension.png)

1. Pour utiliser Xamarin. Forms dans l’extension iOS et le code partagé, vous devez ajouter les références requises :

    - Cliquez avec le bouton droit sur l’extension iOS, sélectionnez **références > ajouter une référence > projets > FormsShareExtension** et appuyez sur **OK**.

    - Cliquez avec le bouton droit sur l’extension iOS, sélectionnez **packages > gérer les packages NuGet... > Xamarin. Forms**  , puis appuyez sur **Ajouter un package**.

1. Développez le projet d’extension et modifiez un point d’entrée pour initialiser Xamarin. Forms et créer des pages. Selon les exigences iOS, une extension doit définir le point d’entrée dans **info. plist** en tant que `NSExtensionMainStoryboard` ou `NSExtensionPrincipalClass` . Une fois que le point d’entrée est activé, dans le cas présent, il s’agit de la `ActionViewController.ViewDidLoad` méthode, vous pouvez créer une instance d’une page Xamarin. Forms et l’afficher à un utilisateur. Par conséquent, ouvrez le point d’entrée et remplacez la `ViewDidLoad` méthode par l’implémentation suivante :

    ```csharp
    public override void ViewDidLoad()
    {
        base.ViewDidLoad();

        // Initialize Xamarin.Forms framework
        global::Xamarin.Forms.Forms.Init();
        // Create an instance of XF page with associated View Model
        var xfPage = new MainPage();
        var viewModel = (MainPageViewModel)xfPage.BindingContext;
        viewModel.Message = "Welcome to XF Page created from an iOS Extension";
        // Override the behavior to complete the execution of the Extension when a user press the button
        viewModel.DoCommand = new Command(() => DoneClicked(this));
        // Convert XF page to a native UIViewController which can be consumed by the iOS Extension
        var newController = xfPage.CreateViewController();
        // Present new view controller as a regular view controller
        this.PresentModalViewController(newController, false);
    }
    ```

    `MainPage`Est instancié à l’aide d’un constructeur standard et avant de pouvoir être utilisé dans l’extension, convertissez-le en code natif à `UIViewController` l’aide de la `CreateViewController` méthode d’extension. 
    
    Générez et exécutez l’application :

    ![Créer une extension](extensions-xf-images/3.walkthrough-runapp.png)

    Pour activer l’extension, accédez au navigateur Safari, tapez une adresse Web, par exemple [Microsoft.com](https://microsoft.com), appuyez sur Parcourir, puis appuyez sur l’icône de **partage** au bas de la page pour afficher les extensions d’action disponibles. Dans la liste des extensions disponibles, sélectionnez l’extension **MyAction** en appuyant sur celle-ci :

    ![Créer une extension](extensions-xf-images/4.walkthrough-run1.png) ![Créer une extension](extensions-xf-images/5.walkthrough-run2.png) ![Créer une extension](extensions-xf-images/6.walkthrough-run3.png)

    L’extension est activée et la page Xamarin. Forms s’affiche pour l’utilisateur. Toutes les liaisons et commandes fonctionnent comme dans l’application conteneur.

1. Le contrôleur d’affichage du point d’entrée d’origine est visible, car il est créé et activé par iOS. Pour résoudre ce problème, modifiez le style de présentation modal pour `UIModalPresentationStyle.FullScreen` le nouveau contrôleur en ajoutant le code suivant juste avant l' `PresentModalViewController` appel :

    ```csharp
    newController.ModalPresentationStyle = UIModalPresentationStyle.FullScreen;
    ```

    Générez et exécutez dans le simulateur iOS ou un appareil :

    ![Xamarin. Forms dans l’extension iOS](extensions-xf-images/7.walkthrough-result.png)

    > [!IMPORTANT]
    > Pour la génération de l’appareil, veillez à utiliser les paramètres de build appropriés et la configuration de la **version** , comme [décrit ici](~/iOS/platform/extensions.md#debug-and-release-versions-of-extensions).

## <a name="related-links"></a>Liens connexes

- [Extensions iOS dans Xamarin.iOS](~/iOS/platform/extensions.md)
- [Xamarin. Forms dans les projets natifs Xamarin](~/xamarin-forms/platform/native-forms.md)
- [Optimiser l’efficacité et les performances d’une extension d’application iOS](https://developer.apple.com/library/archive/documentation/General/Conceptual/ExtensibilityPG/ExtensionCreation.html#//apple_ref/doc/uid/TP40014214-CH5-SW7)
- [Exemple de code source](https://github.com/xamcat/xamarin-forms-ios-extension)