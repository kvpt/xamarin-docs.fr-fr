---
title: Créer une application Xamarin.Forms tenant sur une seule page
description: Cet article explique comment créer une application Xamarin.Forms multiplateforme à page unique, qui vous permet d’entrer une note et conserver dans le stockage de l’appareil.
zone_pivot_groups: platform-dev16
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: E8CF05B1-54B9-428B-8518-D068837BD61E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/01/2019
ms.openlocfilehash: 83c71962ceb4a30c2e05d44fffe4d64b54e4883d
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67831932"
---
# <a name="create-a-single-page-xamarinforms-application"></a>Créer une Application Xamarin.Forms de Page unique

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/SinglePage/)

Dans ce démarrage rapide, vous allez apprendre comment :

- Créer une application de Xamarin.Forms multiplateforme.
- Définissez l’interface utilisateur pour une page à l’aide du langage de balisage d’Application eXtensible (XAML).
- Interagir avec les éléments d’interface utilisateur XAML à partir du code.

Le démarrage rapide vous montre comment créer une application Xamarin.Forms multiplateforme, qui vous permet d’entrer une note et conserver dans le stockage de l’appareil. L’application finale est indiquée ci-dessous :

[![](single-page-images/screenshots-sml.png "Application Notes")](single-page-images/screenshots.png#lightbox "Application Notes")

::: zone pivot="windows"

### <a name="prerequisites"></a>Prérequis

- Visual Studio 2019 (dernière version), avec la **développement Mobile en .NET** charge de travail installée.
- Connaissances de C#.
- (facultatif) Un Mac couplé pour générer l’application sur iOS.

Pour plus d’informations sur ces conditions préalables, consultez [Xamarin installation](~/get-started/installation/index.md). Pour plus d’informations sur la connexion de Visual Studio 2019 à un hôte de build Mac, consultez l’article [Appairer avec un Mac pour le développement Xamarin.iOS](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

## <a name="get-started-with-visual-studio-2019"></a>Bien démarrer avec Visual Studio 2019

1. Lancez Visual Studio 2019 et dans la fenêtre de démarrage, cliquez sur **créer un nouveau projet** pour créer un nouveau projet :

    ![](single-page-images/vs/new-solution-2019.png "Nouveau projet")

2. Dans le **créer un nouveau projet** fenêtre, sélectionnez **Mobile** dans le **type de projet** liste déroulante, sélectionnez le **Mobile App (Xamarin.Forms** modèle), puis cliquez sur le **suivant** bouton :

    ![](single-page-images/vs/new-project-2019.png "Modèles de projet multiplateforme")

3. Dans le **configurer votre nouveau projet** fenêtre, définissez la **nom_projet** à **Notes**, choisissez un emplacement approprié pour le projet, puis cliquez sur le **créer** bouton :

    ![](single-page-images/vs/configure-project.png "Configurer votre projet")

    > [!IMPORTANT]
    > Pour les extraits C# et XAML de ce guide de démarrage rapide, la solution doit se nommer **Notes**. L’utilisation d’un autre nom entraîne des erreurs de build quand vous copiez le code à partir de ce guide de démarrage rapide dans la solution.

4. Dans le **nouvelle application multiplateforme** boîte de dialogue, cliquez sur **application vide**, puis cliquez sur le **OK** bouton :

    ![](single-page-images/vs/new-app-2019.png "Nouvelle application multiplateforme")

    Pour plus d’informations sur la bibliothèque .NET Standard créée, consultez la rubrique [Anatomie d’une application Xamarin.Forms](deepdive.md#anatomy-of-a-xamarinforms-application) dans l’article [Immersion dans Xamarin.Forms - démarrage rapide](deepdive.md).

5. Dans l’**Explorateur de solutions**, dans le projet **Notes**, double-cliquez sur **MainPage.xaml** pour l’ouvrir :

    ![](single-page-images/vs/open-mainpage-xaml-2019.png "Ouvrir MainPage.xaml")

6. Dans **MainPage.xaml**, supprimez tout le code du modèle et remplacez-le par le code suivant :

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

    Ce code définit de manière déclarative l’interface utilisateur pour la page, ce qui se compose d’un [ `Label` ](xref:Xamarin.Forms.Label) pour afficher le texte, un [ `Editor` ](xref:Xamarin.Forms.Editor) pour l’entrée de texte et deux [ `Button` ](xref:Xamarin.Forms.Button) instances qui dirigent l’application à enregistrer ou supprimer un fichier. Les deux instances `Button` sont disposées horizontalement dans une [`Grid`](xref:Xamarin.Forms.Grid), tandis que `Label`, `Editor` et `Grid` sont disposés verticalement dans une [`StackLayout`](xref:Xamarin.Forms.StackLayout). Pour plus d’informations sur la création de l’interface utilisateur, consultez [interface utilisateur](deepdive.md#user-interface) dans le [présentation approfondie du Guide de démarrage rapide Xamarin.Forms](deepdive.md).

    Enregistrez les modifications apportées à **MainPage.xaml** en appuyant sur **Ctrl+S** et fermez le fichier.

7. Dans l’**Explorateur de solutions**, dans le projet **Notes**, développez **MainPage.xaml**, puis double-cliquez sur **MainPage.xaml.cs** pour l’ouvrir :

    ![](single-page-images/vs/open-mainpage-codebehind-2019.png "Ouvrir MainPage.xaml.cs")

8. Dans **MainPage.xaml.cs**, supprimez tout le code du modèle et remplacez-le par le code suivant :

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

    Ce code définit un champ `_fileName`, qui référence un fichier nommé `notes.txt` qui stocke les données de notes dans le dossier de données d’application locale pour l’application. Quand le constructeur de page est exécuté, le fichier est lu, s’il existe, et affiché dans l’[`Editor`](xref:Xamarin.Forms.Editor). Quand le [`Button`](xref:Xamarin.Forms.Button) **Enregistrer** est enfoncé, le gestionnaire d’événements `OnSaveButtonClicked` est exécuté, ce qui enregistre le contenu de l’`Editor` dans le fichier. Quand le `Button` **Supprimer** est enfoncé, le gestionnaire d’événements `OnDeleteButtonClicked` est exécuté, ce qui supprime le fichier, s’il existe, et supprime tout le texte de l’`Editor`. Pour plus d’informations sur l’interaction de l’utilisateur, consultez [répond à une interaction utilisateur](deepdive.md#responding-to-user-interaction) dans le [présentation approfondie du Guide de démarrage rapide Xamarin.Forms](deepdive.md).

    Enregistrez les modifications apportées à **MainPage.xaml.cs** en appuyant sur **Ctrl+S** et fermez le fichier.

### <a name="building-the-quickstart"></a>Génération du guide de démarrage rapide

1. Dans Visual Studio, sélectionnez l’élément de menu **Générer > Générer la solution** (ou appuyez sur F6). La solution est générée et un message de réussite s’affiche dans la barre d’état Visual Studio :

      ![](single-page-images/vs/build-succeeded.png "Build réussie")

    En cas d’erreurs, répétez les étapes précédentes et corrigez les erreurs éventuelles jusqu’à ce que la solution soit générée.

2. Dans la barre d’outils de Visual Studio, appuyez sur le bouton **Démarrer** (le bouton triangulaire qui ressemble à un bouton Lire) pour lancer l’application dans l’émulateur Android de votre choix :

    ![](single-page-images/vs/android-start.png "Barre d’outils Android de Visual Studio")

    [![](single-page-images/vs/notes-android.png "Notes de publication dans l’émulateur Android")](single-page-images/vs/notes-android-large.png#lightbox "Notes in the Android Simulator")

    Entrez une note et appuyez sur le bouton **Enregistrer**.

    Pour plus d’informations sur la façon dont l’application est lancée sur chaque plateforme, consultez [lancement de l’application sur chaque plateforme](deepdive.md#launching-the-application-on-each-platform) dans le [présentation approfondie du Guide de démarrage rapide Xamarin.Forms](deepdive.md).

    > [!NOTE]
    > Vous ne devez exécuter les étapes suivantes que si vous avez un [Mac couplé](~/ios/get-started/installation/windows/connecting-to-mac/index.md) qui répond à la configuration système requise pour le développement Xamarin.Forms.

3. Dans la barre d’outils Visual Studio, cliquez avec le bouton droit sur le projet **Notes.iOS**, puis sélectionnez **Définir comme projet de démarrage**.

      ![](single-page-images/vs/set-as-startup-project-ios.png "Définir iOS comme projet de démarrage")

4. Dans la barre d’outils de Visual Studio, appuyez sur le bouton **Démarrer** (le bouton triangulaire qui ressemble à un bouton Lire) pour lancer l’application dans le [simulateur distant iOS](~/tools/ios-simulator/index.md) de votre choix :

    ![](single-page-images/vs/ios-start.png "Barre d’outils iOS Visual Studio")

    [![](single-page-images/vs/notes-ios.png "Notes de publication dans le simulateur iOS")](single-page-images/vs/notes-ios-large.png#lightbox "Notes dans le simulateur iOS")

    Entrez une note et appuyez sur le bouton **Enregistrer**.

    Pour plus d’informations sur la façon dont l’application est lancée sur chaque plateforme, consultez [lancement de l’application sur chaque plateforme](deepdive.md#launching-the-application-on-each-platform) dans le [présentation approfondie du Guide de démarrage rapide Xamarin.Forms](deepdive.md).

::: zone-end
::: zone pivot="win-vs2017"

### <a name="prerequisites"></a>Prérequis

- Visual Studio 2017, avec le **développement Mobile en .NET** charge de travail installée.
- Connaissances de C#.
- (facultatif) Un Mac couplé pour générer l’application sur iOS.

Pour plus d’informations sur ces conditions préalables, consultez [Xamarin installation](~/get-started/installation/index.md). Pour plus d’informations sur la connexion de Visual Studio 2019 à un hôte de build Mac, consultez l’article [Appairer avec un Mac pour le développement Xamarin.iOS](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

## <a name="get-started-with-visual-studio-2017"></a>Bien démarrer avec Visual Studio 2017

1. Lancez Visual Studio 2017, puis dans la page de démarrage sur **créer un nouveau projet...**  pour créer un nouveau projet :

    ![](single-page-images/vs/new-solution.png "Nouveau projet")

2. Dans la boîte de dialogue **Nouveau projet**, cliquez sur **Multiplateforme**, sélectionnez le modèle **Application mobile (Xamarin.Forms)** , définissez le Nom **Notes**, choisissez l’emplacement du projet et cliquez sur le bouton **OK** :

    ![](single-page-images/vs/new-project.png "Modèles de projet multiplateforme")

    > [!IMPORTANT]
    > Pour les extraits C# et XAML de ce guide de démarrage rapide, la solution doit se nommer **Notes**. L’utilisation d’un autre nom entraîne des erreurs de build quand vous copiez le code à partir de ce guide de démarrage rapide dans la solution.

3. Dans la boîte de dialogue **Nouvelle application multiplateforme**, cliquez sur **Application vide**, sélectionnez **.NET Standard** comme stratégie de partage de code et cliquez sur le bouton **OK** :

    ![](single-page-images/vs/new-app.png "Nouvelle application multiplateforme")

    Pour plus d’informations sur la bibliothèque .NET Standard créée, consultez la rubrique [Anatomie d’une application Xamarin.Forms](deepdive.md#anatomy-of-a-xamarinforms-application) dans l’article [Immersion dans Xamarin.Forms - démarrage rapide](deepdive.md).

4. Dans l’**Explorateur de solutions**, dans le projet **Notes**, double-cliquez sur **MainPage.xaml** pour l’ouvrir :

    ![](single-page-images/vs/open-mainpage-xaml.png "Ouvrir MainPage.xaml")

5. Dans **MainPage.xaml**, supprimez tout le code du modèle et remplacez-le par le code suivant :

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

    Ce code définit de manière déclarative l’interface utilisateur pour la page, ce qui se compose d’un [ `Label` ](xref:Xamarin.Forms.Label) pour afficher le texte, un [ `Editor` ](xref:Xamarin.Forms.Editor) pour l’entrée de texte et deux [ `Button` ](xref:Xamarin.Forms.Button) instances qui dirigent l’application à enregistrer ou supprimer un fichier. Les deux instances `Button` sont disposées horizontalement dans une [`Grid`](xref:Xamarin.Forms.Grid), tandis que `Label`, `Editor` et `Grid` sont disposés verticalement dans une [`StackLayout`](xref:Xamarin.Forms.StackLayout). Pour plus d’informations sur la création de l’interface utilisateur, consultez [interface utilisateur](deepdive.md#user-interface) dans le [présentation approfondie du Guide de démarrage rapide Xamarin.Forms](deepdive.md).

    Enregistrez les modifications apportées à **MainPage.xaml** en appuyant sur **Ctrl+S** et fermez le fichier.

6. Dans l’**Explorateur de solutions**, dans le projet **Notes**, développez **MainPage.xaml**, puis double-cliquez sur **MainPage.xaml.cs** pour l’ouvrir :

    ![](single-page-images/vs/open-mainpage-codebehind.png "Ouvrir MainPage.xaml.cs")

7. Dans **MainPage.xaml.cs**, supprimez tout le code du modèle et remplacez-le par le code suivant :

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

    Ce code définit un champ `_fileName`, qui référence un fichier nommé `notes.txt` qui stocke les données de notes dans le dossier de données d’application locale pour l’application. Quand le constructeur de page est exécuté, le fichier est lu, s’il existe, et affiché dans l’[`Editor`](xref:Xamarin.Forms.Editor). Quand le [`Button`](xref:Xamarin.Forms.Button) **Enregistrer** est enfoncé, le gestionnaire d’événements `OnSaveButtonClicked` est exécuté, ce qui enregistre le contenu de l’`Editor` dans le fichier. Quand le `Button` **Supprimer** est enfoncé, le gestionnaire d’événements `OnDeleteButtonClicked` est exécuté, ce qui supprime le fichier, s’il existe, et supprime tout le texte de l’`Editor`. Pour plus d’informations sur l’interaction de l’utilisateur, consultez [répond à une interaction utilisateur](deepdive.md#responding-to-user-interaction) dans le [présentation approfondie du Guide de démarrage rapide Xamarin.Forms](deepdive.md).

    Enregistrez les modifications apportées à **MainPage.xaml.cs** en appuyant sur **Ctrl+S** et fermez le fichier.

### <a name="building-the-quickstart"></a>Génération du guide de démarrage rapide

1. Dans Visual Studio, sélectionnez l’élément de menu **Générer > Générer la solution** (ou appuyez sur F6). La solution est générée et un message de réussite s’affiche dans la barre d’état Visual Studio :

      ![](single-page-images/vs/build-succeeded.png "Build réussie")

    En cas d’erreurs, répétez les étapes précédentes et corrigez les erreurs éventuelles jusqu’à ce que la solution soit générée.

2. Dans la barre d’outils de Visual Studio, appuyez sur le bouton **Démarrer** (le bouton triangulaire qui ressemble à un bouton Lire) pour lancer l’application dans l’émulateur Android de votre choix :

    ![](single-page-images/vs/android-start.png "Barre d’outils Android de Visual Studio")

    [![](single-page-images/vs/notes-android.png "Notes de publication dans l’émulateur Android")](single-page-images/vs/notes-android-large.png#lightbox "Notes in the Android Simulator")

    Entrez une note et appuyez sur le bouton **Enregistrer**.

    Pour plus d’informations sur la façon dont l’application est lancée sur chaque plateforme, consultez [lancement de l’application sur chaque plateforme](deepdive.md#launching-the-application-on-each-platform) dans le [présentation approfondie du Guide de démarrage rapide Xamarin.Forms](deepdive.md).

    > [!NOTE]
    > Vous ne devez exécuter les étapes suivantes que si vous avez un [Mac couplé](~/ios/get-started/installation/windows/connecting-to-mac/index.md) qui répond à la configuration système requise pour le développement Xamarin.Forms.

3. Dans la barre d’outils Visual Studio, cliquez avec le bouton droit sur le projet **Notes.iOS**, puis sélectionnez **Définir comme projet de démarrage**.

      ![](single-page-images/vs/set-as-startup-project-ios.png "Définir iOS comme projet de démarrage")

4. Dans la barre d’outils de Visual Studio, appuyez sur le bouton **Démarrer** (le bouton triangulaire qui ressemble à un bouton Lire) pour lancer l’application dans le [simulateur distant iOS](~/tools/ios-simulator/index.md) de votre choix :

    ![](single-page-images/vs/ios-start.png "Barre d’outils iOS Visual Studio")

    [![](single-page-images/vs/notes-ios.png "Notes de publication dans le simulateur iOS")](single-page-images/vs/notes-ios-large.png#lightbox "Notes dans le simulateur iOS")

    Entrez une note et appuyez sur le bouton **Enregistrer**.

    Pour plus d’informations sur la façon dont l’application est lancée sur chaque plateforme, consultez [lancement de l’application sur chaque plateforme](deepdive.md#launching-the-application-on-each-platform) dans le [présentation approfondie du Guide de démarrage rapide Xamarin.Forms](deepdive.md).

::: zone-end
::: zone pivot="macos"

### <a name="prerequisites"></a>Prérequis

- Visual Studio pour Mac (dernière version), iOS et la prise en charge de plateforme Android installée.
- Xcode (dernière version).
- Connaissances de C#.

Pour plus d’informations sur ces conditions préalables, consultez [Xamarin installation](~/get-started/installation/index.md).

## <a name="get-started-with-visual-studio-for-mac"></a>Bien démarrer avec Visual Studio pour Mac

1. Cliquez sur le lancement de Visual Studio pour Mac et dans la fenêtre de démarrage **New** pour créer un nouveau projet :

    ![](single-page-images/vsmac/new-project.png "Nouvelle solution")

2. Dans la boîte de dialogue **Choisir un modèle pour votre nouveau projet**, cliquez sur **Multiplateforme > Application**, sélectionnez le modèle **Application Forms vide**, puis cliquez sur le bouton **Suivant** :

    ![](single-page-images/vsmac/choose-template.png "Choisir un modèle")

3. Dans la boîte de dialogue **Configurer une application Forms vide**, nommez la nouvelle application **Notes**, vérifiez que la case d’option **Utiliser .NET Standard** est sélectionnée, puis cliquez sur le bouton **Suivant** :    

    ![](single-page-images/vsmac/configure-app.png "Configurer l’application Formulaires")

4. Dans la boîte de dialogue **Configurer votre nouvelle application Forms vide**, laissez **Notes** comme noms de la solution et du projet, choisissez un emplacement approprié pour le projet, puis cliquez sur le bouton **Créer** pour créer le projet :

    ![](single-page-images/vsmac/configure-project.png "Configurer le projet Formulaires")

    > [!IMPORTANT]
    > Pour les extraits C# et XAML de ce guide de démarrage rapide, la solution et le projet doivent se nommer **Notes**. L’utilisation d’un autre nom entraîne des erreurs de build quand vous copiez le code à partir de ce guide de démarrage rapide dans le projet.

    Pour plus d’informations sur la bibliothèque .NET Standard créée, consultez la rubrique [Anatomie d’une application Xamarin.Forms](deepdive.md#anatomy-of-a-xamarinforms-application) dans l’article [Immersion dans Xamarin.Forms - démarrage rapide](deepdive.md).

5. Dans le **Panneau Solutions**, dans le projet **Notes**, double-cliquez sur **MainPage.xaml** pour l’ouvrir :

    ![](single-page-images/vsmac/mainpage-xaml.png "MainPage.xaml")

6. Dans **MainPage.xaml**, supprimez tout le code du modèle et remplacez-le par le code suivant :

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

    Ce code définit de manière déclarative l’interface utilisateur pour la page, ce qui se compose d’un [ `Label` ](xref:Xamarin.Forms.Label) pour afficher le texte, un [ `Editor` ](xref:Xamarin.Forms.Editor) pour l’entrée de texte et deux [ `Button` ](xref:Xamarin.Forms.Button) instances qui dirigent l’application à enregistrer ou supprimer un fichier. Les deux instances `Button` sont disposées horizontalement dans une [`Grid`](xref:Xamarin.Forms.Grid), tandis que `Label`, `Editor` et `Grid` sont disposés verticalement dans une [`StackLayout`](xref:Xamarin.Forms.StackLayout). Pour plus d’informations sur la création de l’interface utilisateur, consultez [interface utilisateur](deepdive.md#user-interface) dans le [présentation approfondie du Guide de démarrage rapide Xamarin.Forms](deepdive.md).

    Enregistrez les modifications apportées à **MainPage.xaml** en choisissant **Fichier > Enregistrer** (ou en appuyant sur **&#8984;+S**) et fermez le fichier.

7. Dans le **Panneau Solutions**, dans le projet **Notes**, développez **MainPage.xaml**, puis double-cliquez sur **MainPage.xaml.cs** pour l’ouvrir :

    ![](single-page-images/vsmac/mainpage-xaml-cs.png "MainPage.xaml.cs")

8. Dans **MainPage.xaml.cs**, supprimez tout le code du modèle et remplacez-le par le code suivant :

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

    Ce code définit un champ `_fileName`, qui référence un fichier nommé `notes.txt` qui stocke les données de notes dans le dossier de données d’application locale pour l’application. Quand le constructeur de page est exécuté, le fichier est lu, s’il existe, et affiché dans l’[`Editor`](xref:Xamarin.Forms.Editor). Quand le [`Button`](xref:Xamarin.Forms.Button) **Enregistrer** est enfoncé, le gestionnaire d’événements `OnSaveButtonClicked` est exécuté, ce qui enregistre le contenu de l’`Editor` dans le fichier. Quand le `Button` **Supprimer** est enfoncé, le gestionnaire d’événements `OnDeleteButtonClicked` est exécuté, ce qui supprime le fichier, s’il existe, et supprime tout le texte de l’`Editor`. Pour plus d’informations sur l’interaction de l’utilisateur, consultez [répond à une interaction utilisateur](deepdive.md#responding-to-user-interaction) dans le [présentation approfondie du Guide de démarrage rapide Xamarin.Forms](deepdive.md).

    Enregistrez les modifications apportées à **MainPage.xaml.cs** en choisissant **Fichier > Enregistrer** (ou en appuyant sur **&#8984;+S**) et fermez le fichier.

### <a name="building-the-quickstart"></a>Génération du guide de démarrage rapide

1. Dans Visual Studio pour Mac, sélectionnez l’élément de menu **Générer > Générer tout** (ou appuyez sur **&#8984;+B**). Les projets sont générés et un message de réussite s’affiche dans la barre d’outils Visual Studio pour Mac.

      ![](single-page-images/vsmac/build-successful.png "Génération réussie")

    En cas d’erreurs, répétez les étapes précédentes et corrigez les erreurs éventuelles jusqu’à ce que les projets soient générés.

2. Dans le **panneau solutions**, sélectionnez le **Notes.iOS** projet, avec le bouton droit et sélectionnez **définir comme projet de démarrage**:

      ![](single-page-images/vsmac/set-startup-project-ios.png "Définir iOS comme projet de démarrage")

3. Dans la barre d’outils Visual Studio pour Mac, appuyez sur le bouton **Démarrer** (le bouton triangulaire qui ressemble à un bouton Lire) pour lancer l’application dans le simulateur iOS de votre choix :

      ![](single-page-images/vsmac/start.png "Barre d’outils Visual Studio pour Mac")

      [![](single-page-images/vsmac/notes-ios.png "Notes de publication dans le simulateur iOS")](single-page-images/vsmac/notes-ios-large.png#lightbox "Notes dans le simulateur iOS")

    Entrez une note et appuyez sur le bouton **Enregistrer**.

    Pour plus d’informations sur la façon dont l’application est lancée sur chaque plateforme, consultez [lancement de l’application sur chaque plateforme](deepdive.md#launching-the-application-on-each-platform) dans le [présentation approfondie du Guide de démarrage rapide Xamarin.Forms](deepdive.md).

4. Dans le **panneau solutions**, sélectionnez le **Notes.Droid** projet, avec le bouton droit et sélectionnez **définir comme projet de démarrage**:

      ![](single-page-images/vsmac/set-startup-project-android.png "Définir Android comme projet de démarrage")

5. Dans la barre d’outils Visual Studio pour Mac, appuyez sur le bouton **Démarrer** (le bouton triangulaire qui ressemble à un bouton Lire) pour lancer l’application dans l’émulateur Android de votre choix :

      [![](single-page-images/vsmac/notes-android.png "Notes de publication dans l’émulateur Android")](single-page-images/vsmac/notes-android-large.png#lightbox "Notes in the Android Simulator")

    Entrez une note et appuyez sur le bouton **Enregistrer**.

    Pour plus d’informations sur la façon dont l’application est lancée sur chaque plateforme, consultez [lancement de l’application sur chaque plateforme](deepdive.md#launching-the-application-on-each-platform) dans le [présentation approfondie du Guide de démarrage rapide Xamarin.Forms](deepdive.md).

::: zone-end

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez appris comment :

- Créer une application de Xamarin.Forms multiplateforme.
- Définissez l’interface utilisateur pour une page à l’aide du langage de balisage d’Application eXtensible (XAML).
- Interagir avec les éléments d’interface utilisateur XAML à partir du code.

Pour activer cette application à page unique dans une application comportant plusieurs page, passez au démarrage rapide suivant.

> [!div class="nextstepaction"]
> [Next](multi-page.md)

## <a name="related-links"></a>Liens connexes

- [Notes (exemple)](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/SinglePage/)
- [Immersion dans Xamarin.Forms-démarrage rapide](deepdive.md)
