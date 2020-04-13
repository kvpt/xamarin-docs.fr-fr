---
title: Configuration de la plate-forme WPF
description: Xamarin.Forms a maintenant un aperçu de la plate-forme WPF
ms.prod: xamarin
ms.assetid: 650723F2-4279-4B7B-B0A1-D7F8FF26BF1E
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 04/09/2020
ms.openlocfilehash: c9ec9fec2391d7d7a24f97f2ec20208a7d69dbc1
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80992349"
---
# <a name="wpf-platform-setup"></a>Configuration de la plate-forme WPF

![PRÉVERSION](~/media/shared/preview.png)

Xamarin.Forms a maintenant un aperçu de la Windows Presentation Foundation (WPF). Cet article montre comment ajouter un projet WPF à une solution Xamarin.Forms.

> [!IMPORTANT]
> Xamarin.Forms soutien pour WPF est fourni par la communauté. Pour plus d’informations, voir [Xamarin.Forms Platform Support](https://github.com/xamarin/Xamarin.Forms/wiki/Platform-Support).

Avant de commencer, créez une nouvelle solution Xamarin.Forms dans Visual Studio 2019, ou utilisez une solution Xamarin.Forms existante, par [**exemple, BoxViewClock**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-boxviewclock). Vous ne pouvez ajouter que des applications WPF à une solution Xamarin.Forms dans Windows.

## <a name="add-a-wpf-application"></a>Ajouter une application WPF

Suivez ces instructions pour ajouter une application WPF qui s’exécutera sur les ordinateurs de bureau Windows 7, 8 et 10 :

1. Dans Visual Studio 2019, cliquez à droite sur le nom de la solution dans la **Solution Explorer** et choisissez Add > **New Project...**.

2. Dans la **fenêtre Ajouter un nouveau projet,** sélectionnez C **dans** les **langues** baissez, sélectionnez **Windows** dans les **plates-formes** baissez, et sélectionnez **bureau** dans le type **de projet** tomber vers le bas. Dans la liste des types de projets, choisissez **L’application WPF (.NET Framework)**:

    ![Ajouter un nouveau projet WPF](wpf-images/add-project.png "Ajouter un nouveau projet WPF")

    Appuyez sur le bouton **Suivant.**

3. Dans la configuration de votre nouvelle fenêtre **de projet,** tapez un nom pour le projet avec une extension **WPF,** par exemple, **BoxViewClock.WPF**. Cliquez sur le bouton **Parcourir,** sélectionnez le dossier **BoxViewClock** et appuyez sur **Select Folder** pour mettre le projet WPF dans le même répertoire que les autres projets de la solution :

    ![Ajouter un nouveau projet WPF](wpf-images/configure-project.png "Ajouter un nouveau projet WPF")

    Appuyez sur le bouton **Créer** pour créer le projet.

4. Dans la **Solution Explorer**, cliquez à droite sur le nouveau projet **BoxViewClock.WPF** et **sélectionnez Manage NuGet Packages...**. Sélectionnez l’onglet **Parcourir** et recherchez **Xamarin.Forms.Platform.WPF**:

    ![Sélectionnez le forfait NuGet](wpf-images/select-nuget-package.png "Sélectionnez le forfait NuGet")

    Sélectionnez le paquet et cliquez sur le bouton **Installer.**

5. Cliquez à droite sur le nom de la solution dans la **solution Explorer** et **sélectionnez Manage NuGet Packages pour solution...**. Sélectionnez l’onglet **Mises à jour,** puis sélectionnez le forfait **Xamarin.Forms.** Sélectionnez tous les projets et mettez-les à jour pour la même version Xamarin.Forms :

    ![Mettre à jour le paquet NuGet](wpf-images/update-nuget-package.png "Mettre à jour le paquet NuGet")

6. Dans le projet WPF, cliquez à droite sur **références** et sélectionnez **Ajouter référence...**. Dans le dialogue **du gestionnaire de référence,** sélectionnez **les projets** à gauche et vérifiez la case à cocher adjacente au projet **BoxViewClock** :

    ![Référence au projet partagé](wpf-images/reference-shared-project.png "Référence au projet partagé")

    Appuyez sur le bouton **OK.**

7. Modifier le fichier **MainWindow.xaml** du projet WPF. Dans `Window` l’étiquette, ajoutez une déclaration de namespace XML pour l’assemblage **Xamarin.Forms.Platform.WPF** et l’espace nom:

    ```xaml
    xmlns:wpf="clr-namespace:Xamarin.Forms.Platform.WPF;assembly=Xamarin.Forms.Platform.WPF"
    ```

    Maintenant, `Window` changer `wpf:FormsApplicationPage`l’étiquette pour . Modifier `Title` le paramètre pour le nom de votre application, par exemple, **BoxViewClock**. Le fichier XAML terminé devrait ressembler à ceci :

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

8. Modifier le **fichier MainWindow.xaml.cs** du projet WPF. Ajouter deux `using` nouvelles directives :

    ```csharp
    using Xamarin.Forms;
    using Xamarin.Forms.Platform.WPF;
    ```

    Changer la classe `MainWindow` `Window` de `FormsApplicationPage`base de de . Après `InitializeComponent` l’appel, ajoutez les deux énoncés suivants :

    ```csharp
    Forms.Init();
    LoadApplication(new BoxViewClock.App());
    ```

    À l’exception `using` des commentaires et des directives inutilisées, le fichier **MainWindows.xaml.cs** complet devrait ressembler à ceci :

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

9. Cliquez à droite sur le projet WPF dans le **Solution Explorer** et sélectionnez Set comme **Startup Project**. Appuyez sur F5 pour exécuter le programme avec le debugger Visual Studio sur le bureau Windows:

    ![Horloge WPF BoxView](wpf-images/wpf-boxviewclock.png "Horloge WPF BoxView" )

## <a name="platform-specifics"></a>Spécificités de la plate-forme

Vous pouvez déterminer quelle plate-forme votre application Xamarin.Forms fonctionne à partir du code ou de XAML. Cela vous permet de modifier les caractéristiques du programme lorsqu’il est en cours d’exécution sur WPF. En code, comparez `Device.RuntimePlatform` la `Device.WPF` valeur de la constante (qui égale la chaîne "WPF"). S’il y a une correspondance, l’application est en cours d’exécution sur WPF.

Dans XAML, vous `OnPlatform` pouvez utiliser l’étiquette pour sélectionner une valeur de propriété spécifique à la plate-forme :

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

Vous pouvez ajuster la taille initiale de la fenêtre dans le fichier WPF **MainWindow.xaml** :

```xaml
Title="BoxViewClock" Height="450" Width="800"
```

## <a name="issues"></a>Problèmes

Il s’agit d’un aperçu, donc vous devez vous attendre à ce que tout n’est pas prêt à la production. Tous les paquets NuGet pour Xamarin.Forms ne sont pas prêts pour WPF, et certaines fonctionnalités pourraient ne pas fonctionner pleinement.

## <a name="related-video"></a>Vidéo connexe

> [!VIDEO https://youtube.com/embed/Fy9N6OSxK64]

**Xamarin.Forms 3.0 WPF vidéo de soutien**
