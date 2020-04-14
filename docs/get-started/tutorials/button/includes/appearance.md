---
ms.openlocfilehash: 1d2bed830af97ce1ff329a5396a415247a43189d
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/09/2020
ms.locfileid: "61372928"
---
# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

1. Dans **MainPage.xaml**, modifiez la déclaration [`Button`](xref:Xamarin.Forms.Button) pour modifier son apparence visuelle :

    ```xaml
    <Button Text="Click me"
            Clicked="OnButtonClicked"
            TextColor="Blue"
            BackgroundColor="Aqua"
            BorderColor="Red"
            BorderWidth="5"
            CornerRadius="5"
            WidthRequest="150"
            HeightRequest="75" />
    ```

    Ce code définit les propriétés qui modifient l’apparence visuelle de [`Button`](xref:Xamarin.Forms.Button). La propriété [`TextColor`](xref:Xamarin.Forms.Button.TextColor) définit la couleur du texte `Button` et la propriété [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) définit la couleur de l’arrière-plan du texte. La propriété [`BorderColor`](xref:Xamarin.Forms.Button.BorderColor) définit la couleur de la zone qui entoure l’élément `Button`, et la propriété [`BorderWidth`](xref:Xamarin.Forms.Button.BorderWidth) définit la largeur de la bordure. Par défaut, l’élément `Button` est rectangulaire, mais vous pouvez arrondir ses angles en définissant la propriété [`CornerRadius`](xref:Xamarin.Forms.Button.CornerRadius) sur une valeur adéquate. En outre, la taille de l’élément `Button` peut être modifiée en définissant les propriétés [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) et [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest).

1. Dans la barre d’outils Visual Studio, appuyez sur le bouton **Démarrer** (le bouton triangulaire qui ressemble à un bouton de lecture) pour lancer l’application à l’intérieur du simulateur iOS distant ou de l’émulateur Android de votre choix. Vous remarquerez que l’apparence de [`Button`](xref:Xamarin.Forms.Button) a été modifiée :

    [![Capture d’écran d’un bouton dont l’apparence a changé, sur iOS et Android](../images/change-button-appearance.png "Bouton dont l’apparence a changé")](../images/change-button-appearance-large.png#lightbox "Bouton dont l’apparence a changé")

    Pour plus d’informations sur la configuration de l’apparence de l’élément [`Button`](xref:Xamarin.Forms.Button), consultez la rubrique [Apparence du bouton](~/xamarin-forms/user-interface/button.md#button-appearance) du guide [Bouton Xamarin.Forms](~/xamarin-forms/user-interface/button.md).

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/vsmac)

1. Dans **MainPage.xaml**, modifiez la déclaration [`Button`](xref:Xamarin.Forms.Button) pour modifier son apparence visuelle :

    ```xaml
    <Button Text="Click me"
            Clicked="OnButtonClicked"
            TextColor="Blue"
            BackgroundColor="Aqua"
            BorderColor="Red"
            BorderWidth="5"
            CornerRadius="5"
            WidthRequest="150"
            HeightRequest="75" />
    ```

    Ce code définit les propriétés qui modifient l’apparence visuelle de [`Button`](xref:Xamarin.Forms.Button). La propriété [`TextColor`](xref:Xamarin.Forms.Button.TextColor) définit la couleur du texte `Button` et la propriété [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) définit la couleur de l’arrière-plan du texte. La propriété [`BorderColor`](xref:Xamarin.Forms.Button.BorderColor) définit la couleur de la zone qui entoure l’élément `Button`, et la propriété [`BorderWidth`](xref:Xamarin.Forms.Button.BorderWidth) définit la largeur de la bordure. Par défaut, l’élément `Button` est rectangulaire, mais vous pouvez arrondir ses angles en définissant la propriété [`CornerRadius`](xref:Xamarin.Forms.Button.CornerRadius) sur une valeur adéquate. En outre, la taille de l’élément `Button` peut être modifiée en définissant les propriétés [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) et [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest).

1. Dans la barre d’outils Visual Studio pour Mac, appuyez sur le bouton **Démarrer** (le bouton triangulaire qui ressemble à un bouton de lecture) pour lancer l’application dans le simulateur iOS ou dans l’émulateur Android de votre choix. Vous remarquerez que l’apparence de [`Button`](xref:Xamarin.Forms.Button) a été modifiée :

    [![Capture d’écran d’un bouton dont l’apparence a changé, sur iOS et Android](../images/change-button-appearance.png "Bouton dont l’apparence a changé")](../images/change-button-appearance-large.png#lightbox "Bouton dont l’apparence a changé")

    Pour plus d’informations sur la configuration de l’apparence de l’élément [`Button`](xref:Xamarin.Forms.Button), consultez la rubrique [Apparence du bouton](~/xamarin-forms/user-interface/button.md#button-appearance) du guide [Bouton Xamarin.Forms](~/xamarin-forms/user-interface/button.md).
