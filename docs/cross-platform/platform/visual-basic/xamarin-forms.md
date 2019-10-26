---
title: Xamarin. Forms à l’aide de Visual Basic.NET
description: Le modèle de projet Xamarin. Forms peut être modifié pour utiliser Visual Basic pour l’assembly principal, ce qui vous permet de créer des applications mobiles interplateformes à l’aide de VB.NET.
ms.prod: xamarin
ms.assetid: da4b4ba9-9205-47dc-8bae-23272ede2c50
author: conceptdev
ms.author: crdun
ms.date: 04/24/2019
ms.openlocfilehash: ed7e1d65ed361a94ce72a724d797309b40ef8b6c
ms.sourcegitcommit: f8583585c501607fdfa061b95e9a9f385ed1d591
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/26/2019
ms.locfileid: "72959144"
---
# <a name="xamarinforms-using-visual-basicnet"></a>Xamarin. Forms à l’aide de Visual Basic.NET

Xamarin ne prend pas en charge Visual Basic directement-suivez les instructions de cette page pour C# créer une solution Xamarin. Forms, puis C# remplacez le projet .NET standard par Visual Basic.

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/mobile-samples/visualbasic-xamarinformsvb/)

[![créer une solution Xamarin. Forms, puis remplacer le projet .NET Standard par Visual Basic](xamarin-forms-images/hero-sml.png)](xamarin-forms-images/hero.png#lightbox)

> [!NOTE]
> Vous devez utiliser Visual Studio sur Windows pour programmer avec Visual Basic.

## <a name="xamarinforms-with-visual-basic-walkthrough"></a>Xamarin. Forms avec Visual Basic procédure pas à pas

Procédez comme suit pour créer un projet Xamarin. Forms simple qui utilise Visual Basic :

1. Dans Visual Studio 2019, choisissez **créer un nouveau projet**.

2. Dans la fenêtre **créer un nouveau projet** , tapez **Xamarin. Forms** pour filtrer la liste et sélectionnez **application mobile (Xamarin. Forms)** , puis appuyez sur **suivant**.

    [Filtre![pour les applications Xamarin. Forms](xamarin-forms-images/02-sml.png)](xamarin-forms-images/02.png#lightbox)

3. Dans l’écran suivant, tapez un nom pour le projet et appuyez sur **créer**.

4. Choisissez le modèle **vide** , puis appuyez sur **OK**:

    [![modèle Xamarin. Forms vide](xamarin-forms-images/04-sml.png)](xamarin-forms-images/04.png#lightbox)

    Cela crée une solution Xamarin. Forms dans Visual Studio, C#à l’aide de. Les étapes suivantes modifient la solution pour utiliser Visual Basic.

5. Cliquez avec le bouton droit sur la solution et choisissez **ajouter > nouveau projet...**

6. Tapez **Visual Basic bibliothèque** pour filtrer les options du projet et choisissez l’option **bibliothèque de classes (.NET standard)** avec l’icône Visual Basic :

    [Filtre de![pour la bibliothèque de Visual Basic](xamarin-forms-images/06-sml.png)](xamarin-forms-images/06.png#lightbox)

7. Dans l’écran suivant, tapez un nom pour le projet et appuyez sur **créer**.

8. Cliquez avec le bouton droit sur le projet Visual Basic et choisissez **Propriétés**, puis modifiez l' **espace de noms par défaut** pour qu’il corresponde aux projets existants C# :

    [![vérifier que l’espace de noms racine Visual Basic correspond à l’application Xamarin. Forms](xamarin-forms-images/07a-sml.png)](xamarin-forms-images/07a.png#lightbox)

9. Cliquez avec le bouton droit sur le nouveau projet de Visual Basic et sélectionnez **gérer les packages NuGet**, puis installez **Xamarin. Forms** et fermez la fenêtre du gestionnaire de package.

    [![les formulaires et fermer la fenêtre du gestionnaire de package](xamarin-forms-images/07b-sml.png)](xamarin-forms-images/07b.png#lightbox)

10. Renommez le fichier **Class1. vb** par défaut en **app. vb**:

    [![renommer la classe et le fichier Class1 par défaut en app](xamarin-forms-images/08.png)](xamarin-forms-images/08.png#lightbox)

11. Collez le code suivant dans le fichier **app. vb** , qui deviendra le point de départ de votre application Xamarin. Forms :

    ```vb
    Imports Xamarin.Forms

    Public Class App
        Inherits Application

        Public Sub New()
            Dim label = New Label With {.HorizontalTextAlignment = TextAlignment.Center,
                                        .FontSize = Device.GetNamedSize(NamedSize.Medium, GetType(Label)),
                                        .Text = "Welcome to Xamarin.Forms with Visual Basic.NET"}

            Dim stack = New StackLayout With {
                .VerticalOptions = LayoutOptions.Center
            }
            stack.Children.Add(label)

            Dim page = New ContentPage
            page.Content = stack
            MainPage = page

        End Sub

    End Class
    ```

12. Mettez à jour les projets Android et iOS afin qu’ils fassent référence à un nouveau C# projet Visual Basic (et non au projet créé par le modèle).
Cliquez avec le bouton droit sur le nœud **références** dans les projets Android et IOS pour ouvrir le **Gestionnaire de références**. Annulez la cocher de la C# Bibliothèque et cochez la bibliothèque Visual Basic (n’oubliez pas, faites-le pour les projets Android et IOS).

    [![supprimer l’ancienne référence de projet, ajoutez une référence de Visual Basic](xamarin-forms-images/10-sml.png)](xamarin-forms-images/10.png#lightbox)

13. Supprimez C# le projet. Ajoutez de nouveaux fichiers **. vb** pour créer votre application Xamarin. Forms. Vous trouverez ci-dessous un modèle pour les nouveaux `ContentPage`dans Visual Basic :

    ```vb
    Imports Xamarin.Forms

    Public Class Page2
    Inherits ContentPage

        Public Sub New()
            Dim label = New Label With {.HorizontalTextAlignment = TextAlignment.Center,
                                        .FontSize = Device.GetNamedSize(NamedSize.Medium, GetType(Label)),
                                        .Text = "Visual Basic ContentPage"}

            Dim stack = New StackLayout With {
                .VerticalOptions = LayoutOptions.Center
            }
            stack.Children.Add(label)

            Content = stack
        End Sub
    End Class
    ```

## <a name="limitations-of-visual-basic-in-xamarinforms"></a>Limitations de Visual Basic dans Xamarin. Forms

Comme indiqué sur la [page Basic.net visuel portable](~/cross-platform/platform/visual-basic/index.md), Xamarin ne prend pas en charge le langage de Visual Basic. Cela signifie que vous pouvez utiliser Visual Basic pour certaines limitations :

- Les pages XAML ne peuvent pas être incluses dans le projet Visual Basic. le générateur code-behind C#ne peut générer que des. Il est possible d’inclure du code XAML dans une bibliothèque de classes C# portable, référencée et distincte, et d’utiliser la liaison de liaison pour remplir les fichiers XAML via des modèles de Visual Basic (un exemple est inclus dans l' [exemple](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/XamarinFormsVB/XamlPages)).

- Les convertisseurs personnalisés ne peuvent pas être écrits en Visual Basic, ils doivent C# être écrits dans les projets de plateforme natifs.

- Les implémentations de service de dépendance ne peuvent pas être écrites dans Visual Basic, C# elles doivent être écrites dans les projets de plateforme natifs.

## <a name="related-links"></a>Liens associés

- [XamarinFormsVB (exemple)](https://docs.microsoft.com/samples/xamarin/mobile-samples/visualbasic-xamarinformsvb/)
- [Développement multiplateforme avec la .NET Framework](https://docs.microsoft.com/dotnet/standard/cross-platform/)
