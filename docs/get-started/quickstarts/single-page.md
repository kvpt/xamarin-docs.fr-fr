---
title: Créer une application à page unique Xamarin.Forms
description: Cet article explique comment créer une application multiplateforme à une seule page Xamarin.Forms , ce qui vous permet d’entrer une note et de la conserver dans le stockage de l’appareil.
zone_pivot_groups: platform-dev16
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: E8CF05B1-54B9-428B-8518-D068837BD61E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/01/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: cc09f93a8d86b96324435bf63cd3d19b51242ed2
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86931803"
---
# <a name="create-a-single-page-xamarinforms-application"></a>Créer une application à page unique Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-singlepage/)

Dans ce guide de démarrage rapide, vous allez apprendre à :

- Créer une Xamarin.Forms application multiplateforme.
- Définir l’interface utilisateur d’une page à l’aide du langage XAML (Extensible Application Markup Language)
- Interagir avec des éléments d’interface utilisateur XAML à partir du code

Le Guide de démarrage rapide vous guide dans la création d’une Xamarin.Forms application multiplateforme, qui vous permet d’entrer une note et de la conserver dans le stockage de l’appareil. L’application finale est indiquée ci-dessous :

[![Application Notes](single-page-images/screenshots-sml.png)](single-page-images/screenshots.png#lightbox "Application Notes")

::: zone pivot="windows"

### <a name="prerequisites"></a>Prérequis

- Installation de Visual Studio 2019 (dernière version) et de la charge de travail **Développement mobile en .NET**.
- Connaissance de C#.
- (facultatif) Un Mac couplé pour générer l’application sur iOS.

Pour plus d’informations sur ces prérequis, consultez [Installation de Xamarin](~/get-started/installation/index.md). Pour plus d’informations sur la connexion de Visual Studio 2019 à un hôte de build Mac, consultez l’article [Appairer avec un Mac pour le développement Xamarin.iOS](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

## <a name="get-started-with-visual-studio-2019"></a>Bien démarrer avec Visual Studio 2019

1. Lancez Visual Studio 2019, puis dans la fenêtre de démarrage, cliquez sur **Créer un projet** pour créer un projet :

    ![Nouveau projet](single-page-images/vs/new-solution-2019.png)

2. Dans la fenêtre **créer un nouveau projet** , sélectionnez **mobile** dans la liste déroulante **type de projet** , sélectionnez le modèle **application mobile ( Xamarin.Forms )** , puis cliquez sur le bouton **suivant** :

    ![Modèles de projet multiplateforme](single-page-images/vs/new-project-2019.png)

3. Dans la fenêtre **Configurer votre nouveau projet**, affectez à **Nom du projet** la valeur **Notes**, choisissez un emplacement approprié pour le projet, puis cliquez sur le bouton **Créer** :

    ![Configurer votre projet](single-page-images/vs/configure-project.png)

    > [!IMPORTANT]
    > Pour les extraits C# et XAML de ce guide de démarrage rapide, la solution doit se nommer **Notes**. L’utilisation d’un autre nom entraîne des erreurs de build quand vous copiez le code à partir de ce guide de démarrage rapide dans la solution.

4. Dans la boîte de dialogue **Nouvelle application multiplateforme**, cliquez sur **Application vide**, puis cliquez sur le bouton **OK** :

    ![Nouvelle application multiplateforme](single-page-images/vs/new-app-2019.png)

    Pour plus d’informations sur la bibliothèque de .NET Standard qui est créée, consultez [anatomie d’une Xamarin.Forms application](deepdive.md#anatomy-of-a-xamarinforms-application) dans la présentation [ Xamarin.Forms approfondie des Démarrages rapides](deepdive.md).

5. Dans l’**Explorateur de solutions**, dans le projet **Notes**, double-cliquez sur **MainPage.xaml** pour l’ouvrir :

    ![Ouvrir MainPage.xaml](single-page-images/vs/open-mainpage-xaml-2019.png)

6. Dans **MainPage. Xaml**, supprimez tout le code du modèle et remplacez-le par le code suivant :

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.MainPage">
        <StackLayout Margin="10,35,10,10">
            <Label Text="Notes"
                   HorizontalOptions="Center"
                   FontAttributes="Bold" />
            <Editor x:Name="editor"
                    Placeholder="Enter your note"
                    HeightRequest="100" />
            <Grid>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>
                <Button Text="Save"
                        Clicked="OnSaveButtonClicked" />
                <Button Grid.Column="1"
                        Text="Delete"
                        Clicked="OnDeleteButtonClicked"/>
            </Grid>
        </StackLayout>
    </ContentPage>
    ```

    Ce code définit de manière déclarative l’interface utilisateur de la page, qui se compose d’un [`Label`](xref:Xamarin.Forms.Label) pour afficher du texte, d’une [`Editor`](xref:Xamarin.Forms.Editor) entrée de texte et de deux [`Button`](xref:Xamarin.Forms.Button) instances qui indiquent à l’application d’enregistrer ou de supprimer un fichier. Les deux `Button` instances sont disposées horizontalement dans un [`Grid`](xref:Xamarin.Forms.Grid) , avec `Label` , `Editor` , et `Grid` disposées verticalement dans un [`StackLayout`](xref:Xamarin.Forms.StackLayout) . Pour plus d’informations sur la création de l’interface utilisateur, consultez [interface utilisateur](deepdive.md#user-interface) dans la présentation [ Xamarin.Forms approfondie des Démarrages rapides](deepdive.md).

    Enregistrez les modifications apportées à **MainPage.xaml** en appuyant sur **Ctrl+S** et fermez le fichier.

7. Dans l’**Explorateur de solutions**, dans le projet **Notes**, développez **MainPage.xaml**, puis double-cliquez sur **MainPage.xaml.cs** pour l’ouvrir :

    ![Ouvrir MainPage.xaml.cs](single-page-images/vs/open-mainpage-codebehind-2019.png)

8. Dans **MainPage.Xaml.cs**, supprimez tout le code du modèle et remplacez-le par le code suivant :

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;

    namespace Notes
    {
        public partial class MainPage : ContentPage
        {
            string _fileName = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "notes.txt");

            public MainPage()
            {
                InitializeComponent();

                if (File.Exists(_fileName))
                {
                    editor.Text = File.ReadAllText(_fileName);
                }
            }

            void OnSaveButtonClicked(object sender, EventArgs e)
            {
                File.WriteAllText(_fileName, editor.Text);
            }

            void OnDeleteButtonClicked(object sender, EventArgs e)
            {
                if (File.Exists(_fileName))
                {
                    File.Delete(_fileName);
                }
                editor.Text = string.Empty;
            }
        }
    }
    ```

    Ce code définit un champ `_fileName`, qui référence un fichier nommé `notes.txt` qui stocke les données de notes dans le dossier de données d’application locale pour l’application. Lors de l’exécution du constructeur de page, le fichier est lu, s’il existe, et affiché dans le [`Editor`](xref:Xamarin.Forms.Editor) . Lorsque l' **enregistrement** [`Button`](xref:Xamarin.Forms.Button) est activé `OnSaveButtonClicked` , le gestionnaire d’événements est exécuté, ce qui permet d’enregistrer le contenu du `Editor` dans le fichier. Lorsque la **suppression** `Button` est activée, le `OnDeleteButtonClicked` Gestionnaire d’événements est exécuté, ce qui supprime le fichier, à condition qu’il existe, et supprime tout texte de `Editor` . Pour plus d’informations sur l’interaction avec l’utilisateur, consultez [réponse à l’interaction](deepdive.md#responding-to-user-interaction) de l’utilisateur dans la présentation [ Xamarin.Forms approfondie des Démarrages rapides](deepdive.md).

    Enregistrez les modifications apportées à **MainPage.xaml.cs** en appuyant sur **Ctrl+S** et fermez le fichier.

### <a name="building-the-quickstart"></a>Génération du guide de démarrage rapide

1. Dans Visual Studio, sélectionnez l’élément de menu **Générer > Générer la solution** (ou appuyez sur F6). La solution est générée et un message de réussite s’affiche dans la barre d’état Visual Studio :

      ![Build réussie](single-page-images/vs/build-succeeded.png)

    En cas d’erreurs, répétez les étapes précédentes et corrigez les erreurs éventuelles jusqu’à ce que la solution soit générée.

2. Dans la barre d’outils de Visual Studio, appuyez sur le bouton **Démarrer** (le bouton triangulaire qui ressemble à un bouton Lire) pour lancer l’application dans l’émulateur Android de votre choix :

    ![Barre d’outils Android de Visual Studio](single-page-images/vs/android-start.png)

    [![Notes dans l’Émulateur Android](single-page-images/vs/notes-android.png)](single-page-images/vs/notes-android-large.png#lightbox "Notes dans le simulateur Android")

    Entrez une note et appuyez sur le bouton **Enregistrer**.

    Pour plus d’informations sur la façon dont l’application est lancée sur chaque plateforme, consultez [lancement de l’application sur chaque plateforme](deepdive.md#launching-the-application-on-each-platform) dans le [ Xamarin.Forms didacticiel de démarrage rapide](deepdive.md).

    > [!NOTE]
    > Les étapes suivantes doivent être effectuées uniquement si vous disposez d’un [Mac couplé](~/ios/get-started/installation/windows/connecting-to-mac/index.md) qui répond à la configuration système requise pour le Xamarin.Forms développement.

3. Dans la barre d’outils Visual Studio, cliquez avec le bouton droit sur le projet **Notes.iOS**, puis sélectionnez **Définir comme projet de démarrage**.

      ![Définir iOS en tant que projet de démarrage](single-page-images/vs/set-as-startup-project-ios.png)

4. Dans la barre d’outils de Visual Studio, appuyez sur le bouton **Démarrer** (le bouton triangulaire qui ressemble à un bouton Lire) pour lancer l’application dans le [simulateur distant iOS](~/tools/ios-simulator/index.md) de votre choix :

    ![Barre d’outils iOS de Visual Studio](single-page-images/vs/ios-start.png)

    [![Notes dans le simulateur iOS](single-page-images/vs/notes-ios.png)](single-page-images/vs/notes-ios-large.png#lightbox "Notes dans le simulateur iOS")

    Entrez une note et appuyez sur le bouton **Enregistrer**.

    Pour plus d’informations sur la façon dont l’application est lancée sur chaque plateforme, consultez [lancement de l’application sur chaque plateforme](deepdive.md#launching-the-application-on-each-platform) dans le [ Xamarin.Forms didacticiel de démarrage rapide](deepdive.md).

::: zone-end
::: zone pivot="win-vs2017"

### <a name="prerequisites"></a>Prérequis

- Installation de Visual Studio 2017 et de la charge de travail **Développement mobile en .NET**.
- Connaissance de C#.
- (facultatif) Un Mac couplé pour générer l’application sur iOS.

Pour plus d’informations sur ces prérequis, consultez [Installation de Xamarin](~/get-started/installation/index.md). Pour plus d’informations sur la connexion de Visual Studio 2019 à un hôte de build Mac, consultez l’article [Appairer avec un Mac pour le développement Xamarin.iOS](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

## <a name="get-started-with-visual-studio-2017"></a>Bien démarrer avec Visual Studio 2017

1. Lancez Visual Studio 2017, puis dans la page de démarrage, cliquez sur **Créer un projet** pour créer un projet :

    ![Nouveau projet](single-page-images/vs/new-solution.png)

2. Dans la boîte de dialogue **nouveau projet** , cliquez sur **multiplateforme**, sélectionnez le modèle **application mobile ( Xamarin.Forms )** , définissez le nom sur **Notes**, choisissez un emplacement approprié pour le projet et cliquez sur le bouton **OK** :

    ![Modèles de projet multiplateforme](single-page-images/vs/new-project.png)

    > [!IMPORTANT]
    > Pour les extraits C# et XAML de ce guide de démarrage rapide, la solution doit se nommer **Notes**. L’utilisation d’un autre nom entraîne des erreurs de build quand vous copiez le code à partir de ce guide de démarrage rapide dans la solution.

3. Dans la boîte de dialogue **Nouvelle application multiplateforme**, cliquez sur **Application vide**, sélectionnez **.NET Standard** comme stratégie de partage de code et cliquez sur le bouton **OK** :

    ![Nouvelle application multiplateforme](single-page-images/vs/new-app.png)

    Pour plus d’informations sur la bibliothèque de .NET Standard qui est créée, consultez [anatomie d’une Xamarin.Forms application](deepdive.md#anatomy-of-a-xamarinforms-application) dans la présentation [ Xamarin.Forms approfondie des Démarrages rapides](deepdive.md).

4. Dans l’**Explorateur de solutions**, dans le projet **Notes**, double-cliquez sur **MainPage.xaml** pour l’ouvrir :

    ![Ouvrir MainPage.xaml](single-page-images/vs/open-mainpage-xaml.png)

5. Dans **MainPage. Xaml**, supprimez tout le code du modèle et remplacez-le par le code suivant :

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.MainPage">
        <StackLayout Margin="10,35,10,10">
            <Label Text="Notes"
                   HorizontalOptions="Center"
                   FontAttributes="Bold" />
            <Editor x:Name="editor"
                    Placeholder="Enter your note"
                    HeightRequest="100" />
            <Grid>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>
                <Button Text="Save"
                        Clicked="OnSaveButtonClicked" />
                <Button Grid.Column="1"
                        Text="Delete"
                        Clicked="OnDeleteButtonClicked"/>
            </Grid>
        </StackLayout>
    </ContentPage>
    ```

    Ce code définit de manière déclarative l’interface utilisateur de la page, qui se compose d’un [`Label`](xref:Xamarin.Forms.Label) pour afficher du texte, d’une [`Editor`](xref:Xamarin.Forms.Editor) entrée de texte et de deux [`Button`](xref:Xamarin.Forms.Button) instances qui indiquent à l’application d’enregistrer ou de supprimer un fichier. Les deux `Button` instances sont disposées horizontalement dans un [`Grid`](xref:Xamarin.Forms.Grid) , avec `Label` , `Editor` , et `Grid` disposées verticalement dans un [`StackLayout`](xref:Xamarin.Forms.StackLayout) . Pour plus d’informations sur la création de l’interface utilisateur, consultez [interface utilisateur](deepdive.md#user-interface) dans la présentation [ Xamarin.Forms approfondie des Démarrages rapides](deepdive.md).

    Enregistrez les modifications apportées à **MainPage.xaml** en appuyant sur **Ctrl+S** et fermez le fichier.

6. Dans l’**Explorateur de solutions**, dans le projet **Notes**, développez **MainPage.xaml**, puis double-cliquez sur **MainPage.xaml.cs** pour l’ouvrir :

    ![Ouvrir MainPage.xaml.cs](single-page-images/vs/open-mainpage-codebehind.png)

7. Dans **MainPage.Xaml.cs**, supprimez tout le code du modèle et remplacez-le par le code suivant :

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;

    namespace Notes
    {
        public partial class MainPage : ContentPage
        {
            string _fileName = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "notes.txt");

            public MainPage()
            {
                InitializeComponent();

                if (File.Exists(_fileName))
                {
                    editor.Text = File.ReadAllText(_fileName);
                }
            }

            void OnSaveButtonClicked(object sender, EventArgs e)
            {
                File.WriteAllText(_fileName, editor.Text);
            }

            void OnDeleteButtonClicked(object sender, EventArgs e)
            {
                if (File.Exists(_fileName))
                {
                    File.Delete(_fileName);
                }
                editor.Text = string.Empty;
            }
        }
    }
    ```

    Ce code définit un champ `_fileName`, qui référence un fichier nommé `notes.txt` qui stocke les données de notes dans le dossier de données d’application locale pour l’application. Lors de l’exécution du constructeur de page, le fichier est lu, s’il existe, et affiché dans le [`Editor`](xref:Xamarin.Forms.Editor) . Lorsque l' **enregistrement** [`Button`](xref:Xamarin.Forms.Button) est activé `OnSaveButtonClicked` , le gestionnaire d’événements est exécuté, ce qui permet d’enregistrer le contenu du `Editor` dans le fichier. Lorsque la **suppression** `Button` est activée, le `OnDeleteButtonClicked` Gestionnaire d’événements est exécuté, ce qui supprime le fichier, à condition qu’il existe, et supprime tout texte de `Editor` . Pour plus d’informations sur l’interaction avec l’utilisateur, consultez [réponse à l’interaction](deepdive.md#responding-to-user-interaction) de l’utilisateur dans la présentation [ Xamarin.Forms approfondie des Démarrages rapides](deepdive.md).

    Enregistrez les modifications apportées à **MainPage.xaml.cs** en appuyant sur **Ctrl+S** et fermez le fichier.

### <a name="building-the-quickstart"></a>Génération du guide de démarrage rapide

1. Dans Visual Studio, sélectionnez l’élément de menu **Générer > Générer la solution** (ou appuyez sur F6). La solution est générée et un message de réussite s’affiche dans la barre d’état Visual Studio :

      ![Build réussie](single-page-images/vs/build-succeeded.png)

    En cas d’erreurs, répétez les étapes précédentes et corrigez les erreurs éventuelles jusqu’à ce que la solution soit générée.

2. Dans la barre d’outils de Visual Studio, appuyez sur le bouton **Démarrer** (le bouton triangulaire qui ressemble à un bouton Lire) pour lancer l’application dans l’émulateur Android de votre choix :

    ![Barre d’outils Android de Visual Studio](single-page-images/vs/android-start.png)

    [![Notes dans l’Émulateur Android](single-page-images/vs/notes-android.png)](single-page-images/vs/notes-android-large.png#lightbox "Notes dans le simulateur Android")

    Entrez une note et appuyez sur le bouton **Enregistrer**.

    Pour plus d’informations sur la façon dont l’application est lancée sur chaque plateforme, consultez [lancement de l’application sur chaque plateforme](deepdive.md#launching-the-application-on-each-platform) dans le [ Xamarin.Forms didacticiel de démarrage rapide](deepdive.md).

    > [!NOTE]
    > Les étapes suivantes doivent être effectuées uniquement si vous disposez d’un [Mac couplé](~/ios/get-started/installation/windows/connecting-to-mac/index.md) qui répond à la configuration système requise pour le Xamarin.Forms développement.

3. Dans la barre d’outils Visual Studio, cliquez avec le bouton droit sur le projet **Notes.iOS**, puis sélectionnez **Définir comme projet de démarrage**.

      ![Définir iOS en tant que projet de démarrage](single-page-images/vs/set-as-startup-project-ios.png)

4. Dans la barre d’outils de Visual Studio, appuyez sur le bouton **Démarrer** (le bouton triangulaire qui ressemble à un bouton Lire) pour lancer l’application dans le [simulateur distant iOS](~/tools/ios-simulator/index.md) de votre choix :

    ![Barre d’outils iOS de Visual Studio](single-page-images/vs/ios-start.png)

    [![Notes dans le simulateur iOS](single-page-images/vs/notes-ios.png)](single-page-images/vs/notes-ios-large.png#lightbox "Notes dans le simulateur iOS")

    Entrez une note et appuyez sur le bouton **Enregistrer**.

    Pour plus d’informations sur la façon dont l’application est lancée sur chaque plateforme, consultez [lancement de l’application sur chaque plateforme](deepdive.md#launching-the-application-on-each-platform) dans le [ Xamarin.Forms didacticiel de démarrage rapide](deepdive.md).

::: zone-end
::: zone pivot="macos"

### <a name="prerequisites"></a>Prérequis

- Visual Studio pour Mac (dernière version) avec installation de la prise en charge des plateformes iOS et Android.
- Xcode (dernière version).
- Connaissance de C#.

Pour plus d’informations sur ces prérequis, consultez [Installation de Xamarin](~/get-started/installation/index.md).

## <a name="get-started-with-visual-studio-for-mac"></a>Bien démarrer avec Visual Studio pour Mac

1. Lancez Visual Studio pour Mac, puis dans la fenêtre de démarrage, cliquez sur **Nouveau** pour créer un projet :

    ![Nouvelle solution](single-page-images/vsmac/new-project.png)

2. Dans la boîte de dialogue **Choisir un modèle pour votre nouveau projet**, cliquez sur **Multiplateforme > Application**, sélectionnez le modèle **Application Forms vide**, puis cliquez sur le bouton **Suivant** :

    ![Choisir un modèle](single-page-images/vsmac/choose-template.png)

3. Dans la boîte de dialogue **Configurer une application Forms vide**, nommez la nouvelle application **Notes**, vérifiez que la case d’option **Utiliser .NET Standard** est sélectionnée, puis cliquez sur le bouton **Suivant** :    

    ![Configurer l’application Formulaires](single-page-images/vsmac/configure-app.png)

4. Dans la boîte de dialogue **Configurer votre nouvelle application Forms vide**, laissez **Notes** comme noms de la solution et du projet, choisissez un emplacement approprié pour le projet, puis cliquez sur le bouton **Créer** pour créer le projet :

    ![Configurer le projet Formulaires](single-page-images/vsmac/configure-project.png)

    > [!IMPORTANT]
    > Pour les extraits C# et XAML de ce guide de démarrage rapide, la solution et le projet doivent se nommer **Notes**. L’utilisation d’un autre nom entraîne des erreurs de build quand vous copiez le code à partir de ce guide de démarrage rapide dans le projet.

    Pour plus d’informations sur la bibliothèque de .NET Standard qui est créée, consultez [anatomie d’une Xamarin.Forms application](deepdive.md#anatomy-of-a-xamarinforms-application) dans la présentation [ Xamarin.Forms approfondie des Démarrages rapides](deepdive.md).

5. Dans le **Panneau Solutions**, dans le projet **Notes**, double-cliquez sur **MainPage.xaml** pour l’ouvrir :

    ![MainPage.xaml](single-page-images/vsmac/mainpage-xaml.png)

6. Dans **MainPage. Xaml**, supprimez tout le code du modèle et remplacez-le par le code suivant :

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.MainPage">
        <StackLayout Margin="10,35,10,10">
            <Label Text="Notes"
                   HorizontalOptions="Center"
                   FontAttributes="Bold" />
            <Editor x:Name="editor"
                    Placeholder="Enter your note"
                    HeightRequest="100" />
            <Grid>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>
                <Button Text="Save"
                        Clicked="OnSaveButtonClicked" />
                <Button Grid.Column="1"
                        Text="Delete"
                        Clicked="OnDeleteButtonClicked"/>
            </Grid>
        </StackLayout>
    </ContentPage>
    ```

    Ce code définit de manière déclarative l’interface utilisateur de la page, qui se compose d’un [`Label`](xref:Xamarin.Forms.Label) pour afficher du texte, d’une [`Editor`](xref:Xamarin.Forms.Editor) entrée de texte et de deux [`Button`](xref:Xamarin.Forms.Button) instances qui indiquent à l’application d’enregistrer ou de supprimer un fichier. Les deux `Button` instances sont disposées horizontalement dans un [`Grid`](xref:Xamarin.Forms.Grid) , avec `Label` , `Editor` , et `Grid` disposées verticalement dans un [`StackLayout`](xref:Xamarin.Forms.StackLayout) . Pour plus d’informations sur la création de l’interface utilisateur, consultez [interface utilisateur](deepdive.md#user-interface) dans la présentation [ Xamarin.Forms approfondie des Démarrages rapides](deepdive.md).

    Enregistrez les modifications apportées à **MainPage.xaml** en choisissant **Fichier > Enregistrer** (ou en appuyant sur **&#8984;+S**) et fermez le fichier.

7. Dans le **Panneau Solutions**, dans le projet **Notes**, développez **MainPage.xaml**, puis double-cliquez sur **MainPage.xaml.cs** pour l’ouvrir :

    ![MainPage.xaml.cs](single-page-images/vsmac/mainpage-xaml-cs.png)

8. Dans **MainPage.Xaml.cs**, supprimez tout le code du modèle et remplacez-le par le code suivant :

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;

    namespace Notes
    {
        public partial class MainPage : ContentPage
        {
            string _fileName = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "notes.txt");

            public MainPage()
            {
                InitializeComponent();

                if (File.Exists(_fileName))
                {
                    editor.Text = File.ReadAllText(_fileName);
                }
            }

            void OnSaveButtonClicked(object sender, EventArgs e)
            {
                File.WriteAllText(_fileName, editor.Text);
            }

            void OnDeleteButtonClicked(object sender, EventArgs e)
            {
                if (File.Exists(_fileName))
                {
                    File.Delete(_fileName);
                }
                editor.Text = string.Empty;
            }
        }
    }
    ```

    Ce code définit un champ `_fileName`, qui référence un fichier nommé `notes.txt` qui stocke les données de notes dans le dossier de données d’application locale pour l’application. Lors de l’exécution du constructeur de page, le fichier est lu, s’il existe, et affiché dans le [`Editor`](xref:Xamarin.Forms.Editor) . Lorsque l' **enregistrement** [`Button`](xref:Xamarin.Forms.Button) est activé `OnSaveButtonClicked` , le gestionnaire d’événements est exécuté, ce qui permet d’enregistrer le contenu du `Editor` dans le fichier. Lorsque la **suppression** `Button` est activée, le `OnDeleteButtonClicked` Gestionnaire d’événements est exécuté, ce qui supprime le fichier, à condition qu’il existe, et supprime tout texte de `Editor` . Pour plus d’informations sur l’interaction avec l’utilisateur, consultez [réponse à l’interaction](deepdive.md#responding-to-user-interaction) de l’utilisateur dans la présentation [ Xamarin.Forms approfondie des Démarrages rapides](deepdive.md).

    Enregistrez les modifications apportées à **MainPage.xaml.cs** en choisissant **Fichier > Enregistrer** (ou en appuyant sur **&#8984;+S**) et fermez le fichier.

### <a name="building-the-quickstart"></a>Génération du guide de démarrage rapide

1. Dans Visual Studio pour Mac, sélectionnez l’élément de menu **Générer > Générer tout** (ou appuyez sur **&#8984;+B**). Les projets sont générés et un message de réussite s’affiche dans la barre d’outils Visual Studio pour Mac.

      ![Build réussie](single-page-images/vsmac/build-successful.png)

    En cas d’erreurs, répétez les étapes précédentes et corrigez les erreurs éventuelles jusqu’à ce que les projets soient générés.

2. Dans le **Panneau Solutions**, sélectionnez le projet **Notes.iOS**, cliquez avec le bouton droit, puis sélectionnez **Définir en tant que projet de démarrage** :

      ![Définir iOS en tant que projet de démarrage](single-page-images/vsmac/set-startup-project-ios.png)

3. Dans la barre d’outils Visual Studio pour Mac, appuyez sur le bouton **Démarrer** (le bouton triangulaire qui ressemble à un bouton Lire) pour lancer l’application dans le simulateur iOS de votre choix :

      ![Barre d’outils Visual Studio pour Mac](single-page-images/vsmac/start.png)

      [![Notes dans le simulateur iOS](single-page-images/vsmac/notes-ios.png)](single-page-images/vsmac/notes-ios-large.png#lightbox "Notes dans le simulateur iOS")

    Entrez une note et appuyez sur le bouton **Enregistrer**.

    Pour plus d’informations sur la façon dont l’application est lancée sur chaque plateforme, consultez [lancement de l’application sur chaque plateforme](deepdive.md#launching-the-application-on-each-platform) dans le [ Xamarin.Forms didacticiel de démarrage rapide](deepdive.md).

4. Dans le **Panneau Solutions**, sélectionnez le projet **Notes.Droid**, cliquez avec le bouton droit, puis sélectionnez **Définir en tant que projet de démarrage** :

      ![Définir Android en tant que projet de démarrage](single-page-images/vsmac/set-startup-project-android.png)

5. Dans la barre d’outils Visual Studio pour Mac, appuyez sur le bouton **Démarrer** (le bouton triangulaire qui ressemble à un bouton Lire) pour lancer l’application dans l’émulateur Android de votre choix :

      [![Notes dans l’Émulateur Android](single-page-images/vsmac/notes-android.png)](single-page-images/vsmac/notes-android-large.png#lightbox "Notes dans le simulateur Android")

    Entrez une note et appuyez sur le bouton **Enregistrer**.

    Pour plus d’informations sur la façon dont l’application est lancée sur chaque plateforme, consultez [lancement de l’application sur chaque plateforme](deepdive.md#launching-the-application-on-each-platform) dans le [ Xamarin.Forms didacticiel de démarrage rapide](deepdive.md).

::: zone-end

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez appris comment :

- Créer une Xamarin.Forms application multiplateforme.
- Définir l’interface utilisateur d’une page à l’aide du langage XAML (Extensible Application Markup Language)
- Interagir avec des éléments d’interface utilisateur XAML à partir du code

Pour transformer cette application monopage en application multipage, passez au prochain guide de démarrage rapide.

> [!div class="nextstepaction"]
> [Suivant](multi-page.md)

## <a name="related-links"></a>Liens connexes

- [Notes (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-singlepage/)
- [Xamarin.FormsPrésentation approfondie des Démarrages rapides](deepdive.md)
