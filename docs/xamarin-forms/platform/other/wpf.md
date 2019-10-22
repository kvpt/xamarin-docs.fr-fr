---
title: Configuration de la plateforme WPF
description: Xamarin. Forms prend désormais en charge la version préliminaire de la plateforme WPF
ms.prod: xamarin
ms.assetid: 650723F2-4279-4B7B-B0A1-D7F8FF26BF1E
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 04/05/2018
ms.openlocfilehash: 38d9b42b3a29ea46d05a1d1cc4e38641d2445786
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2019
ms.locfileid: "71997170"
---
# <a name="wpf-platform-setup"></a>Configuration de la plateforme WPF

![Aperçu](~/media/shared/preview.png)

Xamarin. Forms dispose désormais de la prise en charge de l’Aperçu pour le Windows Presentation Foundation (WPF). Cet article explique comment ajouter un projet WPF à une solution Xamarin. Forms.

> [!IMPORTANT]
> La prise en charge de Xamarin. Forms pour WPF est fournie par la communauté. Pour plus d’informations, consultez [prise en charge de la plateforme Xamarin. Forms](https://github.com/xamarin/Xamarin.Forms/wiki/Platform-Support).

Avant de commencer, créez une nouvelle solution Xamarin. Forms dans Visual Studio 2019, ou utilisez une solution Xamarin. Forms existante, par exemple, [**BoxViewClock**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-boxviewclock). Vous pouvez uniquement ajouter des applications WPF à une solution Xamarin. Forms dans Windows.

## <a name="add-a-wpf-project-to-a-xamarinforms-app-with-xamarinuniversity"></a>Ajouter un projet WPF à une application Xamarin. Forms avec Xamarin. University

> [!VIDEO https://youtube.com/embed/Fy9N6OSxK64]

**Vidéo sur la prise en charge de WPF Xamarin. Forms 3,0**

## <a name="adding-a-wpf-app"></a>Ajout d’une application WPF

Suivez ces instructions pour ajouter une application WPF qui s’exécutera sur les ordinateurs de bureau Windows 7, 8 et 10 :

1. Dans Visual Studio 2019, cliquez avec le bouton droit sur le nom de la solution dans le **Explorateur de solutions** puis sélectionnez **Ajouter > nouveau projet...** .

2. Dans la fenêtre **nouveau projet** , sur le côté gauche, sélectionnez **visuel C#**  et **Bureau classique Windows**. Dans la liste des types de projets, choisissez **application WPF (.NET Framework)** .

3. Tapez un nom pour le projet avec une extension **WPF** , par exemple, **BoxViewClock. WPF**. Cliquez sur le bouton **Parcourir** , sélectionnez le dossier **BoxViewClock** , puis appuyez sur **Sélectionner un dossier**. Le projet WPF est alors placé dans le même répertoire que les autres projets de la solution.

    ![Ajouter un nouveau projet WPF](wpf-images/add-new-project.png "Ajouter un nouveau projet WPF")

    Appuyez sur OK pour créer le projet.

4. Dans le **Explorateur de solutions**, cliquez avec le bouton droit sur le nouveau projet **BoxViewClock. WPF** , puis sélectionnez **gérer les packages NuGet**. Sélectionnez l’onglet **Parcourir** , cliquez sur la case à cocher **inclure la version préliminaire** , puis recherchez **Xamarin. Forms**.

    ![Sélectionner le package NuGet](wpf-images/select-nuget-package.png "Sélectionner le package NuGet")

    Sélectionnez ce package, puis cliquez sur le bouton **installer** .

5. À présent, recherchez le package **Xamarin. Forms. Platform. WPF** et installez-le également. Assurez-vous que le package provient de Microsoft.

6. Cliquez avec le bouton droit sur le nom de la solution dans le **Explorateur de solutions** puis sélectionnez **gérer les packages NuGet pour la solution**. Sélectionnez l’onglet **mettre à jour** et le package **Xamarin. Forms** . Sélectionnez tous les projets et mettez-les à jour vers la même version de Xamarin. Forms :

    ![Mettre à jour le package NuGet](wpf-images/update-nuget-package.png "Mettre à jour le package NuGet")

7. Dans le projet WPF, cliquez avec le bouton droit sur **références**. Dans la boîte de dialogue **Gestionnaire de références** , sélectionnez **projets** à gauche, puis cochez la case située en regard du projet **BoxViewClock** :

    ![Référencer le projet partagé](wpf-images/reference-shared-project.png "Référencer le projet partagé")

8. Modifiez le fichier **MainWindow. Xaml** du projet WPF. Dans la balise `Window`, ajoutez une déclaration d’espace de noms XML pour l’assembly et l’espace de noms **Xamarin. Forms. Platform. WPF** :

    ```xaml
    xmlns:wpf="clr-namespace:Xamarin.Forms.Platform.WPF;assembly=Xamarin.Forms.Platform.WPF"
    ```

    À présent, remplacez la balise `Window` par `wpf:FormsApplicationPage`. Remplacez le paramètre `Title` par le nom de votre application, par exemple **BoxViewClock**. Le fichier XAML complet doit se présenter comme suit :

    ```xaml
    <wpf:FormsApplicationPage x:Class="BoxViewClock.WPF.MainWindow"
            xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
            xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
            xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
            xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
            xmlns:wpf="clr-namespace:Xamarin.Forms.Platform.WPF;assembly=Xamarin.Forms.Platform.WPF"
            xmlns:local="clr-namespace:BoxViewClock.WPF"
            mc:Ignorable="d"
            Title="BoxViewClock" Height="450" Width="800">
        <Grid>

        </Grid>
    </wpf:FormsApplicationPage>
    ```

9. Modifiez le fichier **MainWindow.Xaml.cs** du projet WPF. Ajoutez deux nouvelles directives de `using` :

    ```csharp
    using Xamarin.Forms;
    using Xamarin.Forms.Platform.WPF;
    ```

    Remplacez la classe de base de `MainWindow` `Window` par `FormsApplicationPage`. À la suite de l’appel de `InitializeComponent`, ajoutez les deux instructions suivantes :

    ```csharp
    Forms.Init();
    LoadApplication(new BoxViewClock.App());
    ```

    À l’exception des commentaires et des directives de `using` inutilisées, le fichier **MainWindows.Xaml.cs** complet doit ressembler à ceci :

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

10. Cliquez avec le bouton droit sur le projet WPF dans le **Explorateur de solutions** , puis sélectionnez **définir comme projet de démarrage**. Appuyez sur F5 pour exécuter le programme avec le débogueur Visual Studio sur le bureau Windows :

    ![Horloge BoxView WPF](wpf-images/wpf-boxviewclock.png "Horloge BoxView WPF" )

## <a name="next-steps"></a>Étapes suivantes

### <a name="platform-specifics"></a>Caractéristiques de la plateforme

Vous pouvez déterminer quelle plateforme est exécutée par votre application Xamarin. Forms à partir du code ou du code XAML. Cela vous permet de modifier les caractéristiques du programme lorsqu’il s’exécute sur WPF. Dans le code, comparez la valeur de `Device.RuntimePlatform` avec la constante `Device.WPF` (qui est égale à la chaîne « WPF »). En cas de correspondance, l’application s’exécute sur WPF.

En XAML, vous pouvez utiliser la balise `OnPlatform` pour sélectionner une valeur de propriété spécifique à la plateforme :

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

### <a name="window-size"></a>Taille de la fenêtre

Vous pouvez ajuster la taille initiale de la fenêtre dans le fichier **. Xaml MAINWINDOW** WPF :

```xaml
Title="BoxViewClock" Height="450" Width="800"
```

## <a name="issues"></a>Questions

Il s’agit d’une version préliminaire. vous devez donc vous attendre à ce que tous les éléments soient prêts pour la production. Tous les packages NuGet pour Xamarin. Forms sont prêts pour WPF, et certaines fonctionnalités peuvent ne pas être entièrement opérationnelles.
