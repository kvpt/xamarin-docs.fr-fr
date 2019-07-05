---
ms.openlocfilehash: 5a464196c08220158432219d96bf82578789d8e5
ms.sourcegitcommit: 8ecfa339d0f3e7687977bfe4fc96448942690183
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67559993"
---
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Dans **MainPage.xaml**, modifiez la déclaration [`StackLayout`](xref:Xamarin.Forms.StackLayout) pour en aligner les éléments enfants à l’horizontale plutôt qu’à la verticale :

    ```xaml
    <StackLayout Margin="20,35,20,25"
                 Orientation="Horizontal">
        <Label Text="The StackLayout has its Margin property set, to control the rendering position of the StackLayout." />
        <Label Text="The Padding property can be set to specify the distance between the StackLayout and its children." />
        <Label Text="The Spacing property can be set to specify the distance between views in the StackLayout." />
    </StackLayout>
    ```

    Ce code définit la propriété [`Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) sur [`Horizontal`](xref:Xamarin.Forms.StackOrientation.Horizontal).

1. Dans la barre d’outils Visual Studio, appuyez sur le bouton **Démarrer** (le bouton triangulaire qui ressemble à un bouton de lecture) pour lancer l’application à l’intérieur du simulateur iOS distant ou de l’émulateur Android de votre choix :

    [![Capture d’écran d’affichages enfants orientés horizontalement dans un StackLayout sur iOS et Android](../images/orientation.png "StackLayout contenant des instances Label orientées horizontalement")](../images/orientation-large.png#lightbox "StackLayout contenant des instances Label orientées horizontalement")

    Notez que les instances [`Label`](xref:Xamarin.Forms.Label) dans le [`StackLayout`](xref:Xamarin.Forms.StackLayout) sont maintenant alignées horizontalement, au lieu de verticalement.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio pour Mac](#tab/vsmac)

1. Dans **MainPage.xaml**, modifiez la déclaration [`StackLayout`](xref:Xamarin.Forms.StackLayout) pour en aligner les éléments enfants à l’horizontale plutôt qu’à la verticale :

    ```xaml
    <StackLayout Margin="20,35,20,25"
                 Orientation="Horizontal">
        <Label Text="The StackLayout has its Margin property set, to control the rendering position of the StackLayout." />
        <Label Text="The Padding property can be set to specify the distance between the StackLayout and its children." />
        <Label Text="The Spacing property can be set to specify the distance between views in the StackLayout." />
    </StackLayout>
    ```

    Ce code définit la propriété [`Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) sur [`Horizontal`](xref:Xamarin.Forms.StackOrientation.Horizontal).

1. Dans la barre d’outils Visual Studio pour Mac, appuyez sur le bouton **Démarrer** (le bouton triangulaire qui ressemble à un bouton de lecture) pour lancer l’application dans le simulateur iOS ou dans l’émulateur Android de votre choix :

    [![Capture d’écran d’affichages enfants orientés horizontalement dans un StackLayout sur iOS et Android](../images/orientation.png "StackLayout contenant des instances Label orientées horizontalement")](../images/orientation-large.png#lightbox "StackLayout contenant des instances Label orientées horizontalement")

    Notez que les instances [`Label`](xref:Xamarin.Forms.Label) dans le [`StackLayout`](xref:Xamarin.Forms.StackLayout) sont maintenant alignées horizontalement, au lieu de verticalement.

-----

> [!div class="nextstepaction"]
> [J’ai rencontré un problème](https://github.com/MicrosoftDocs/xamarin-docs/issues/new?title=StackLayout+Tutorial+Step+2+Feedback&template=tutorial_template.md)
