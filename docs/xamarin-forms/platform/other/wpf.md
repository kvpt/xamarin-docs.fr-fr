---
title: ''
description: Xamarin.Formsprend désormais en charge la version préliminaire de la plateforme WPF
ms.prod: ''
ms.assetid: ''
ms.technology: ''
ms.custom: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: bbb9ea950c9f1daa1b5ee4762527d689f3cdd483
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84139527"
---
# <a name="wpf-platform-setup"></a>Configuration de la plateforme WPF

![PRÉVERSION](~/media/shared/preview.png)

Xamarin.Formsprend désormais en charge l’Aperçu pour le Windows Presentation Foundation (WPF). Cet article explique comment ajouter un projet WPF à une Xamarin.Forms solution.

> [!IMPORTANT]
> Xamarin.Formsla prise en charge de WPF est fournie par la communauté. Pour plus d’informations, consultez [ Xamarin.Forms prise en charge des plateformes](https://github.com/xamarin/Xamarin.Forms/wiki/Platform-Support).

Avant de commencer, créez une nouvelle Xamarin.Forms solution dans Visual Studio 2019 ou utilisez une solution existante Xamarin.Forms , par exemple [**BoxViewClock**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-boxviewclock). Vous pouvez uniquement ajouter des applications WPF à une Xamarin.Forms solution dans Windows.

## <a name="add-a-wpf-application"></a>Ajouter une application WPF

Suivez ces instructions pour ajouter une application WPF qui s’exécutera sur les ordinateurs de bureau Windows 7, 8 et 10 :

1. Dans Visual Studio 2019, cliquez avec le bouton droit sur le nom de la solution dans le **Explorateur de solutions** puis sélectionnez **Ajouter > nouveau projet...**.

2. Dans la fenêtre **Ajouter un nouveau projet** , sélectionnez **C#** dans la liste déroulante **langues** , sélectionnez **Windows** dans la liste déroulante **plateformes** , puis sélectionnez **Bureau** dans la liste déroulante **type de projet** . Dans la liste des types de projets, choisissez **application WPF (.NET Framework)**:

    ![Ajouter un nouveau projet WPF](wpf-images/add-project.png "Ajouter un nouveau projet WPF")

    Appuyez sur le bouton **suivant** .

3. Dans la fenêtre **configurer votre nouveau projet** , tapez un nom pour le projet avec une extension **WPF** , par exemple, **BoxViewClock. WPF**. Cliquez sur le bouton **Parcourir** , sélectionnez le dossier **BoxViewClock** , puis appuyez sur **Sélectionner un dossier** pour placer le projet WPF dans le même répertoire que les autres projets de la solution :

    ![Ajouter un nouveau projet WPF](wpf-images/configure-project.png "Ajouter un nouveau projet WPF")

    Appuyez sur le bouton **créer** pour créer le projet.

4. Dans le **Explorateur de solutions**, cliquez avec le bouton droit sur le nouveau projet **BoxViewClock. WPF** , puis sélectionnez **gérer les packages NuGet...**. Sélectionnez l’onglet **Parcourir** et recherchez ** Xamarin.Forms . Platform. WPF**:

    ![Sélectionner le package NuGet](wpf-images/select-nuget-package.png "Sélectionner le package NuGet")

    Sélectionnez le package, puis cliquez sur le bouton **installer** .

5. Cliquez avec le bouton droit sur le nom de la solution dans le **Explorateur de solutions** puis sélectionnez **gérer les packages NuGet pour la solution...**. Sélectionnez l’onglet **mises à jour** , puis sélectionnez le **Xamarin.Forms** Package. Sélectionnez tous les projets et mettez-les à jour vers la même Xamarin.Forms version :

    ![Mettre à jour le package NuGet](wpf-images/update-nuget-package.png "Mettre à jour le package NuGet")

6. Dans le projet WPF, cliquez avec le bouton droit sur **références** , puis sélectionnez **Ajouter une référence...**. Dans la boîte de dialogue **Gestionnaire de références** , sélectionnez **projets** à gauche, puis cochez la case située en regard du projet **BoxViewClock** :

    ![Référencer le projet partagé](wpf-images/reference-shared-project.png "Référencer le projet partagé")

    Appuyez sur le bouton **OK** .

7. Modifiez le fichier **MainWindow. Xaml** du projet WPF. Dans la `Window` balise, ajoutez une déclaration d’espace de noms XML pour ** Xamarin.Forms . **Assembly et espace de noms Platform. WPF :

    ```xaml
    xmlns:wpf="clr-namespace:Xamarin.Forms.Platform.WPF;assembly=Xamarin.Forms.Platform.WPF"
    ```

    À présent, remplacez la `Window` balise par `wpf:FormsApplicationPage` . Remplacez le `Title` paramètre par le nom de votre application, par exemple, **BoxViewClock**. Le fichier XAML complet doit se présenter comme suit :

    ```xaml
    <wpf:FormsApplicationPage x:Class="BoxViewClock.WPF.MainWindow"
            xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
            xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
            xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
            xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
            xmlns:local="clr-namespace:BoxViewClock.WPF"
            xmlns:wpf="clr-namespace:Xamarin.Forms.Platform.WPF;assembly=Xamarin.Forms.Platform.WPF"            
            mc:Ignorable="d"
            Title="BoxViewClock" Height="450" Width="800">
        <Grid>

        </Grid>
    </wpf:FormsApplicationPage>
    ```

8. Modifiez le fichier **MainWindow.Xaml.cs** du projet WPF. Ajoutez deux nouvelles `using` directives :

    ```csharp
    using Xamarin.Forms;
    using Xamarin.Forms.Platform.WPF;
    ```

    Remplacez la classe de base `MainWindow` de `Window` par `FormsApplicationPage` . Après l' `InitializeComponent` appel, ajoutez les deux instructions suivantes :

    ```csharp
    Forms.Init();
    LoadApplication(new BoxViewClock.App());
    ```

    À l’exception des commentaires et des directives inutilisées `using` , le fichier **MainWindows.Xaml.cs** complet doit ressembler à ceci :

    ```csharp
    using Xamarin.Forms;
    using Xamarin.Forms.Platform.WPF;

    namespace BoxViewClock.WPF
    {
        public partial class MainWindow : FormsApplicationPage
        {
            public MainWindow()
            {
                InitializeComponent();

                Forms.Init();
                LoadApplication(new BoxViewClock.App());
            }
        }
    }
    ```

9. Cliquez avec le bouton droit sur le projet WPF dans le **Explorateur de solutions** , puis sélectionnez **définir comme projet de démarrage**. Appuyez sur F5 pour exécuter le programme avec le débogueur Visual Studio sur le bureau Windows :

    ![Horloge BoxView WPF](wpf-images/wpf-boxviewclock.png "Horloge BoxView WPF" )

## <a name="platform-specifics"></a>Caractéristiques de la plateforme

Vous pouvez déterminer la plateforme sur laquelle votre Xamarin.Forms application s’exécute, à partir du code ou du code XAML. Cela vous permet de modifier les caractéristiques du programme lorsqu’il s’exécute sur WPF. Dans le code, comparez la valeur de `Device.RuntimePlatform` à la `Device.WPF` constante (qui est égale à la chaîne « WPF »). En cas de correspondance, l’application s’exécute sur WPF.

En XAML, vous pouvez utiliser la `OnPlatform` balise pour sélectionner une valeur de propriété spécifique à la plateforme :

```xaml
<Button.TextColor>
    <OnPlatform x:TypeArguments="Color">
        <On Platform="iOS" Value="White" />
        <On Platform="macOS" Value="White" />
        <On Platform="Android" Value="Black" />
        <On Platform="WPF" Value="Blue" />
    </OnPlatform>
</Button.TextColor>
```

## <a name="window-size"></a>Taille de la fenêtre

Vous pouvez ajuster la taille initiale de la fenêtre dans le fichier **. Xaml MAINWINDOW** WPF :

```xaml
Title="BoxViewClock" Height="450" Width="800"
```

## <a name="issues"></a>Problèmes

Il s’agit d’une version préliminaire. vous devez donc vous attendre à ce que tous les éléments soient prêts pour la production. Tous les packages NuGet pour Xamarin.Forms sont prêts pour WPF, et certaines fonctionnalités peuvent ne pas être entièrement opérationnelles.

## <a name="related-video"></a>Vidéo connexe

> [!VIDEO https://youtube.com/embed/Fy9N6OSxK64]

**Xamarin.Formsvidéo sur la prise en charge de WPF 3,0**
