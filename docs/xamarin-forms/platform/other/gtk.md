---
title: 'Configuration de la plateforme GTK #'
description: 'Xamarin. Forms prend désormais en charge la version préliminaire de la plateforme GTK #'
ms.prod: xamarin
ms.assetid: 3417FB95-3E4B-47DA-85D0-F34832747236
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/10/2018
ms.openlocfilehash: d20e27fdbb1f62c2aebb7122d7f0c042b3ac9085
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2019
ms.locfileid: "71997189"
---
# <a name="gtk-platform-setup"></a>Configuration de la plateforme GTK #

![Aperçu](~/media/shared/preview.png)

Xamarin. Forms prend désormais en charge l’Aperçu pour les applications GTK #. GTK # est un kit d’outils d’interface utilisateur graphique qui lie le GTK + Toolkit et diverses bibliothèques GNOME, permettant ainsi le développement d’applications graphiques GNOME entièrement natives à l’aide de mono et de .NET. Cet article explique comment ajouter un projet GTK # à une solution Xamarin. Forms.

> [!IMPORTANT]
> La prise en charge de Xamarin. Forms pour GTK # est fournie par la communauté. Pour plus d’informations, consultez [prise en charge de la plateforme Xamarin. Forms](https://github.com/xamarin/Xamarin.Forms/wiki/Platform-Support).

Avant de commencer, créez une solution Xamarin. Forms ou utilisez une solution Xamarin. Forms existante, par exemple, [**GameOfLife**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-gameoflife).

> [!NOTE]
> Bien que cet article se concentre sur l’ajout d’une application GTK # à une solution Xamarin. Forms dans VS2017 et Visual Studio pour Mac, elle peut également être effectuée dans [MonoDevelop](http://www.monodevelop.com/) pour Linux.

## <a name="adding-a-gtk-app"></a>Ajout d’une application GTK #

GTK # pour macOS et Linux est installé dans le cadre de [mono](https://www.mono-project.com/download/stable/). GTK # pour .NET peut être installé sur Windows à l’aide du [programme d’installation de GTK #](https://www.mono-project.com/download/stable/#download-win).

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Suivez ces instructions pour ajouter une application GTK # qui s’exécutera sur le bureau Windows :

1. Dans Visual Studio 2019, cliquez avec le bouton droit sur le nom de la solution dans **Explorateur de solutions** puis sélectionnez **Ajouter > nouveau projet...** .

2. Dans la fenêtre **nouveau projet** , sur le côté gauche, sélectionnez **visuel C#**  et **Bureau classique Windows**. Dans la liste des types de projets, choisissez **bibliothèque de classes (.NET Framework)** et assurez-vous que la liste déroulante **Framework** est définie sur un minimum de .NET Framework 4,7.

3. Tapez un nom pour le projet avec une extension **GTK** , par exemple **GameOfLife. GTK**. Cliquez sur le bouton **Parcourir** , sélectionnez le dossier contenant les autres projets de plateforme, puis appuyez sur **Sélectionner un dossier**. Le projet GTK est alors placé dans le même répertoire que les autres projets de la solution.

    ![Ajouter un nouveau projet GTK](gtk-images/win/add-new-project.png "Ajouter un nouveau projet GTK")

    Appuyez sur le bouton **OK** pour créer le projet.

4. Dans le **Explorateur de solutions**, cliquez avec le bouton droit sur le nouveau projet GTK, puis sélectionnez **gérer les packages NuGet**. Sélectionnez l’onglet **Parcourir** , puis recherchez **Xamarin. Forms** 3,0 ou une version ultérieure.

    ![Sélectionner le package NuGet Xamarin. Forms](gtk-images/win/select-forms-nuget-package.png "Sélectionner le package NuGet Xamarin. Forms")

    Sélectionnez le package, puis cliquez sur le bouton **installer** .

5. À présent, recherchez le package **Xamarin. Forms. Platform. GTK** 3,0 ou une version ultérieure.

    ![Sélectionnez le package NuGet Xamarin. Forms. Platform. GTK](gtk-images/win/select-forms-platform-nuget-package.png "Sélectionnez le package NuGet Xamarin. Forms. Platform. GTK")

    Sélectionnez le package, puis cliquez sur le bouton **installer** .

6. Dans le **Explorateur de solutions**, cliquez avec le bouton droit sur le nom de la solution et sélectionnez **gérer les packages NuGet pour la solution**. Sélectionnez l’onglet **mettre à jour** et le package **Xamarin. Forms** . Sélectionnez tous les projets et mettez-les à jour vers la même version de Xamarin. Forms que celle utilisée par le projet GTK.

7. Dans le **Explorateur de solutions**, cliquez avec le bouton droit sur **références** dans le projet GTK. Dans la boîte de dialogue **Gestionnaire de références** , sélectionnez **projets** à gauche, puis cochez la case située en regard de la .NET standard ou du projet partagé :

    ![Référencer le projet partagé](gtk-images/win/reference-shared-project.png "Référencer le projet partagé")

8. Dans la boîte de dialogue **Gestionnaire de références** , appuyez sur le bouton **Parcourir** et accédez au dossier **C:\Program Files (x86) \GtkSharp\2.12\lib** et sélectionnez **ATK-Sharp. dll**, **gdk-Sharp. dll**, **Glade-Sharp. dll**,  **glib-Sharp. dll**, **GTK-dotnet. dll**, **GTK-Sharp. dll** .

    ![Référencer les bibliothèques GTK #](gtk-images/win/reference-gtk-libraries.png "Référencer les bibliothèques GTK #")

    Appuyez sur le bouton **OK** pour ajouter les références.

9. Dans le projet GTK, renommez **Class1.cs** en **Program.cs**.

10. Dans le projet GTK, modifiez le fichier **Program.cs** afin qu’il ressemble au code suivant :

    ```csharp
    using System;
    using Xamarin.Forms;
    using Xamarin.Forms.Platform.GTK;

    namespace GameOfLife.GTK
    {
        class MainClass
        {
            [STAThread]
            public static void Main(string[] args)
            {
                Gtk.Application.Init();
                Forms.Init();

                var app = new App();
                var window = new FormsWindow();
                window.LoadApplication(app);
                window.SetApplicationTitle("Game of Life");
                window.Show();

                Gtk.Application.Run();
            }
        }
    }
    ```

    Ce code initialise GTK # et Xamarin. Forms, crée une fenêtre d’application et exécute l’application.

11. Dans le **Explorateur de solutions**, cliquez avec le bouton droit sur le projet GTK, puis sélectionnez **Propriétés**.

12. Dans la fenêtre **Propriétés** , sélectionnez l’onglet **application** et définissez la liste déroulante **type de sortie** sur **application Windows**.

    ![Modifier le type de sortie du projet](gtk-images/win/change-project-output-type.png "Modifier le type de sortie du projet")

13. Dans le **Explorateur de solutions**, cliquez avec le bouton droit sur le projet GTK et sélectionnez **définir comme projet de démarrage**. Appuyez sur F5 pour exécuter le programme avec le débogueur Visual Studio sur le bureau Windows :

    ![GTK # jeu de vie](gtk-images/win/gtk-gameoflife.png "GTK # jeu de vie")

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

Suivez ces instructions pour ajouter une application GTK # qui s’exécutera sur le bureau Mac :

1. Dans Visual Studio pour Mac, cliquez avec le bouton droit sur la solution Xamarin. Forms, puis choisissez **ajouter > ajouter un nouveau projet...** .

2. Dans la fenêtre **nouveau projet** , choisissez **autre > .net > gtk # 2,0 projet** et appuyez sur **suivant**.

3. Tapez un nom pour le projet avec une extension **GTK** , par exemple **GameOfLife. GTK**, puis appuyez sur **créer**.

4. Dans le **panneau solutions**, cliquez avec le bouton droit sur **packages > ajouter des packages...** pour le projet GTK, puis ajoutez le package NuGet en préversion Xamarin. Forms 3,0 ou version ultérieure.

    ![Sélectionner le package NuGet Xamarin. Forms](gtk-images/mac/select-forms-nuget-package.png "Sélectionner le package NuGet Xamarin. Forms")

5. Dans le **panneau solutions**, cliquez avec le bouton droit sur **packages > ajouter des packages...** pour le projet GTK, puis ajoutez le package NuGet Xamarin. Forms. Platform. gtk 3,0 version préliminaire ou ultérieure.

    ![Sélectionnez le package NuGet Xamarin. Forms. Platform. GTK](gtk-images/mac/select-forms-platform-nuget-package.png "Sélectionnez le package NuGet Xamarin. Forms. Platform. GTK")

6. Mettez à jour les autres projets de plateforme pour utiliser la même version de Xamarin. Forms que celle utilisée par le projet GTK.

7. Dans la **panneau solutions**, cliquez avec le bouton droit sur **références > modifier les références...** pour le projet GTK, puis ajoutez une référence au projet Xamarin. Forms (.NET standard ou projet partagé).

    ![Référencer le projet partagé](gtk-images/mac/reference-shared-project.png "Référencer le projet partagé")

8. Modifiez le fichier **Program.cs** du projet GTK afin qu’il ressemble au code suivant :

    ```csharp
    using System;
    using Xamarin.Forms;
    using Xamarin.Forms.Platform.GTK;

    namespace GameOfLife.GTK
    {
        class MainClass
        {
            [STAThread]
            public static void Main(string[] args)
            {
                Gtk.Application.Init();
                Forms.Init();

                var app = new App();
                var window = new FormsWindow();
                window.LoadApplication(app);
                window.SetApplicationTitle("Game of Life");
                window.Show();

                Gtk.Application.Run();
            }
        }
    }
    ```

    Ce code initialise GTK # et Xamarin. Forms, crée une fenêtre d’application et exécute l’application.

9. Dans le **panneau solutions**, cliquez avec le bouton droit sur le projet GTK et sélectionnez **définir comme projet de démarrage**.

10. Dans la barre d’outils Visual Studio pour Mac, appuyez sur le bouton **Démarrer** (le bouton triangulaire qui ressemble à un bouton de lecture) pour lancer l’application.

    ![GTK # jeu de vie](gtk-images/mac/gtk-gameoflife.png "GTK # jeu de vie")

-----

## <a name="next-steps"></a>Étapes suivantes

### <a name="platform-specifics"></a>Caractéristiques de la plateforme

Vous pouvez déterminer la plateforme sur laquelle votre application Xamarin. Forms s’exécute en XAML ou dans le code. Cela vous permet de modifier les caractéristiques du programme lorsqu’il s’exécute sur GTK #. Dans le code, comparez la valeur de `Device.RuntimePlatform` avec la constante `Device.GTK` (qui est égale à la chaîne « GTK »). En cas de correspondance, l’application s’exécute sur GTK #.

En XAML, vous pouvez utiliser la balise `OnPlatform` pour sélectionner une valeur de propriété spécifique à la plateforme :

```xaml
<Button.TextColor>
    <OnPlatform x:TypeArguments="Color">
        <On Platform="iOS" Value="White" />
        <On Platform="macOS" Value="White" />
        <On Platform="Android" Value="Black" />
        <On Platform="GTK" Value="Blue" />
    </OnPlatform>
</Button.TextColor>
```

### <a name="application-icon"></a>Icône d’application

Vous pouvez définir l’icône de l’application au démarrage :

```csharp
window.SetApplicationIcon("icon.png");
```

### <a name="themes"></a>Thèmes

Il existe un large éventail de thèmes disponibles pour GTK #, et ils peuvent être utilisés à partir d’une application Xamarin. Forms :

```csharp
GtkThemes.Init ();
GtkThemes.LoadCustomTheme ("Themes/gtkrc");
```

### <a name="native-forms"></a>Formulaires natifs

Les formulaires natifs permettent aux pages dérivées de Xamarin. Forms [`ContentPage`](xref:Xamarin.Forms.ContentPage)d’être consommées par les projets natifs, y compris les projets GTK #. Pour ce faire, vous pouvez créer une instance de la page dérivée de [`ContentPage`](xref:Xamarin.Forms.ContentPage)et la convertir en type GTK # natif à l’aide de la méthode d’extension `CreateContainer` :

```csharp
var settingsView = new SettingsView().CreateContainer();
vbox.PackEnd(settingsView, true, true, 0);
```

Pour plus d’informations sur les formulaires natifs, consultez [formulaires natifs](~/xamarin-forms/platform/native-forms.md).

## <a name="issues"></a>Questions

Il s’agit d’une version préliminaire. vous devez donc vous attendre à ce que tous les éléments soient prêts pour la production. Pour connaître l’état actuel de l’implémentation, consultez [État](https://github.com/jsuarezruiz/forms-gtk-progress/blob/master/Status.md)et pour les problèmes connus actuels, consultez [en attente & problèmes connus](https://github.com/jsuarezruiz/forms-gtk-progress/blob/master/Issues-Pending.md).
