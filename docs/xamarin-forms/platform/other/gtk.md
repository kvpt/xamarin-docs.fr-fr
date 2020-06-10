---
titre : « GTK
ms.openlocfilehash: a5635da9f7c083609ce1e0f120d0613fff9bd77b
ms.sourcegitcommit: ea9269b5d9e3d68b61bb428560a10034117ee457
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2020
ms.locfileid: "84198106"
---
# <a name="gtk-platform-setup"></a>Configuration de la plateforme GTK #

![PRÉVERSION](~/media/shared/preview.png)

Xamarin.Formsprend désormais en charge l’Aperçu pour les applications GTK #. GTK # est un kit d’outils d’interface utilisateur graphique qui lie le GTK + Toolkit et diverses bibliothèques GNOME, permettant ainsi le développement d’applications graphiques GNOME entièrement natives à l’aide de mono et de .NET. Cet article explique comment ajouter un projet GTK # à une Xamarin.Forms solution.

> [!IMPORTANT]
> Xamarin.Formsla prise en charge de GTK # est fournie par la communauté. Pour plus d’informations, consultez [ Xamarin.Forms prise en charge des plateformes](https://github.com/xamarin/Xamarin.Forms/wiki/Platform-Support).

Avant de commencer, créez une Xamarin.Forms solution ou utilisez une Xamarin.Forms solution existante, par exemple [**GameOfLife**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-gameoflife).

> [!NOTE]
> Bien que cet article se concentre sur l’ajout d’une application GTK # à une Xamarin.Forms solution dans VS2017 et Visual Studio pour Mac, elle peut également être effectuée dans [MonoDevelop](https://www.monodevelop.com/) pour Linux.

## <a name="adding-a-gtk-app"></a>Ajout d’une application GTK #

GTK # pour macOS et Linux est installé dans le cadre de [mono](https://www.mono-project.com/download/stable/). GTK # pour .NET peut être installé sur Windows à l’aide du [programme d’installation de GTK #](https://www.mono-project.com/download/stable/#download-win).

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Suivez ces instructions pour ajouter une application GTK # qui s’exécutera sur le bureau Windows :

1. Dans Visual Studio 2019, cliquez avec le bouton droit sur le nom de la solution dans **Explorateur de solutions** puis sélectionnez **Ajouter > nouveau projet...**.

2. Dans la fenêtre **nouveau projet** , dans la partie gauche, sélectionnez **Visual C#** et **Bureau classique Windows**. Dans la liste des types de projets, choisissez **bibliothèque de classes (.NET Framework)** et assurez-vous que la liste déroulante **Framework** est définie sur un minimum de .NET Framework 4,7.

3. Tapez un nom pour le projet avec une extension **GTK** , par exemple **GameOfLife. GTK**. Cliquez sur le bouton **Parcourir** , sélectionnez le dossier contenant les autres projets de plateforme, puis appuyez sur **Sélectionner un dossier**. Le projet GTK est alors placé dans le même répertoire que les autres projets de la solution.

    ![Ajouter un nouveau projet GTK](gtk-images/win/add-new-project.png "Ajouter un nouveau projet GTK")

    Appuyez sur le bouton **OK** pour créer le projet.

4. Dans le **Explorateur de solutions**, cliquez avec le bouton droit sur le nouveau projet GTK, puis sélectionnez **gérer les packages NuGet**. Sélectionnez l’onglet **Parcourir** , puis recherchez **Xamarin.Forms** 3,0 ou une version ultérieure.

    ![Sélectionner le Xamarin.Forms package NuGet](gtk-images/win/select-forms-nuget-package.png "Sélectionnez la [ ! Opérationnel. NO-LOC (Xamarin. Forms)] package NuGet")

    Sélectionnez le package, puis cliquez sur le bouton **installer** .

5. Recherchez maintenant ** Xamarin.Forms . Package Platform. GTK** 3,0 ou supérieur.

    ![Sélectionnez le Xamarin.Forms . Package NuGet Platform. GTK](gtk-images/win/select-forms-platform-nuget-package.png "Sélectionnez la [ ! Opérationnel. NO-LOC (Xamarin. Forms)]. Package NuGet Platform. GTK")

    Sélectionnez le package, puis cliquez sur le bouton **installer** .

6. Dans le **Explorateur de solutions**, cliquez avec le bouton droit sur le nom de la solution et sélectionnez **gérer les packages NuGet pour la solution**. Sélectionnez l’onglet **mettre à jour** et le **Xamarin.Forms** Package. Sélectionnez tous les projets et mettez-les à jour vers la même Xamarin.Forms version que celle utilisée par le projet GTK.

7. Dans le **Explorateur de solutions**, cliquez avec le bouton droit sur **références** dans le projet GTK. Dans la boîte de dialogue **Gestionnaire de références** , sélectionnez **projets** à gauche, puis cochez la case située en regard de la .NET standard ou du projet partagé :

    ![Référencer le projet partagé](gtk-images/win/reference-shared-project.png "Référencer le projet partagé")

8. Dans la boîte de dialogue **Gestionnaire de références** , appuyez sur le bouton **Parcourir** et accédez au dossier **C:\Program Files (x86) \GtkSharp\2.12\lib** , puis sélectionnez les fichiers **atk-sharp.dll**, **gdk-sharp.dll**, **glade-sharp.dll**, **glib-sharp.dll**, **gtk-dotnet.dll**, **gtk-sharp.dll** .

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

    Ce code initialise GTK # et Xamarin.Forms crée une fenêtre d’application et exécute l’application.

11. Dans le **Explorateur de solutions**, cliquez avec le bouton droit sur le projet GTK, puis sélectionnez **Propriétés**.

12. Dans la fenêtre **Propriétés** , sélectionnez l’onglet **application** et définissez la liste déroulante **type de sortie** sur **application Windows**.

    ![Modifier le type de sortie du projet](gtk-images/win/change-project-output-type.png "Modifier le type de sortie du projet")

13. Dans le **Explorateur de solutions**, cliquez avec le bouton droit sur le projet GTK et sélectionnez **définir comme projet de démarrage**. Appuyez sur F5 pour exécuter le programme avec le débogueur Visual Studio sur le bureau Windows :

    ![GTK # jeu de vie](gtk-images/win/gtk-gameoflife.png "GTK # jeu de vie")

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

Suivez ces instructions pour ajouter une application GTK # qui s’exécutera sur le bureau Mac :

1. Dans Visual Studio pour Mac, cliquez avec le bouton droit sur la Xamarin.Forms solution et choisissez **Ajouter > ajouter un nouveau projet...**.

2. Dans la fenêtre **nouveau projet** , choisissez **autre > .net > GTK # 2,0 projet** et appuyez sur **suivant**.

3. Tapez un nom pour le projet avec une extension **GTK** , par exemple **GameOfLife. GTK**, puis appuyez sur **créer**.

4. Dans le **panneau solutions**, cliquez avec le bouton droit sur **packages > ajouter des packages...** pour le projet GTK, puis ajoutez le Xamarin.Forms package NuGet version préliminaire 3,0 ou ultérieur.

    ![Sélectionner le Xamarin.Forms package NuGet](gtk-images/mac/select-forms-nuget-package.png "Sélectionnez la [ ! Opérationnel. NO-LOC (Xamarin. Forms)] package NuGet")

5. Dans le **panneau solutions**, cliquez avec le bouton droit sur **packages > ajouter des packages.** .. pour le projet GTK, puis ajoutez Xamarin.Forms . Platform. GTK 3,0 version préliminaire ou ultérieure du package NuGet.

    ![Sélectionnez le Xamarin.Forms . Package NuGet Platform. GTK](gtk-images/mac/select-forms-platform-nuget-package.png "Sélectionnez la [ ! Opérationnel. NO-LOC (Xamarin. Forms)]. Package NuGet Platform. GTK")

6. Mettez à jour les autres projets de plateforme pour utiliser la même Xamarin.Forms version que celle utilisée par le projet GTK.

7. Dans la **panneau solutions**, cliquez avec le bouton droit sur **références > modifier les références...** pour le projet GTK, puis ajoutez une référence au Xamarin.Forms projet (.NET standard ou projet partagé).

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

    Ce code initialise GTK # et Xamarin.Forms crée une fenêtre d’application et exécute l’application.

9. Dans le **panneau solutions**, cliquez avec le bouton droit sur le projet GTK et sélectionnez **définir comme projet de démarrage**.

10. Dans la barre d’outils Visual Studio pour Mac, appuyez sur le bouton **Démarrer** (le bouton triangulaire qui ressemble à un bouton de lecture) pour lancer l’application.

    ![GTK # jeu de vie](gtk-images/mac/gtk-gameoflife.png "GTK # jeu de vie")

-----

## <a name="next-steps"></a>Étapes suivantes

### <a name="platform-specifics"></a>Caractéristiques de la plateforme

Vous pouvez déterminer la plateforme sur laquelle votre Xamarin.Forms application s’exécute en XAML ou dans le code. Cela vous permet de modifier les caractéristiques du programme lorsqu’il s’exécute sur GTK #. Dans le code, comparez la valeur de `Device.RuntimePlatform` à la `Device.GTK` constante (qui est égale à la chaîne « GTK »). En cas de correspondance, l’application s’exécute sur GTK #.

En XAML, vous pouvez utiliser la `OnPlatform` balise pour sélectionner une valeur de propriété spécifique à la plateforme :

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

Il existe un large éventail de thèmes disponibles pour GTK #, et ils peuvent être utilisés à partir d’une Xamarin.Forms application :

```csharp
GtkThemes.Init ();
GtkThemes.LoadCustomTheme ("Themes/gtkrc");
```

### <a name="native-forms"></a>Formulaires natifs

Les formulaires natifs permettent Xamarin.Forms [`ContentPage`](xref:Xamarin.Forms.ContentPage) aux pages dérivées de être consommées par les projets natifs, y compris les projets GTK #. Pour ce faire, vous pouvez créer une instance de la [`ContentPage`](xref:Xamarin.Forms.ContentPage) page dérivée de et la convertir en type GTK # natif à l’aide de la `CreateContainer` méthode d’extension :

```csharp
var settingsView = new SettingsView().CreateContainer();
vbox.PackEnd(settingsView, true, true, 0);
```

Pour plus d’informations sur les formulaires natifs, consultez [formulaires natifs](~/xamarin-forms/platform/native-forms.md).

## <a name="issues"></a>Problèmes

Il s’agit d’une version préliminaire. vous devez donc vous attendre à ce que tous les éléments soient prêts pour la production. Pour connaître l’état actuel de l’implémentation, consultez [État](https://github.com/jsuarezruiz/forms-gtk-progress/blob/master/Status.md)et pour les problèmes connus actuels, consultez [en attente & problèmes connus](https://github.com/jsuarezruiz/forms-gtk-progress/blob/master/Issues-Pending.md).
