---
ms.openlocfilehash: a7c2aa15521b89e4930746dc5421ce67fa26b2b9
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/09/2020
ms.locfileid: "67559993"
---
# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

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

    [![Capture d’écran de vues enfants orientées horizontalement dans une disposition de pile, sur iOS et Android](../images/orientation.png "Disposition de pile contenant des instances d’étiquettes orientées horizontalement")](../images/orientation-large.png#lightbox "Disposition de pile contenant des instances d’étiquettes orientées horizontalement")

    Notez que les instances [`Label`](xref:Xamarin.Forms.Label) dans le [`StackLayout`](xref:Xamarin.Forms.StackLayout) sont maintenant alignées horizontalement, au lieu de verticalement.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/vsmac)

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

    [![Capture d’écran de vues enfants orientées horizontalement dans une disposition de pile, sur iOS et Android](../images/orientation.png "Disposition de pile contenant des instances d’étiquettes orientées horizontalement")](../images/orientation-large.png#lightbox "Disposition de pile contenant des instances d’étiquettes orientées horizontalement")

    Notez que les instances [`Label`](xref:Xamarin.Forms.Label) dans le [`StackLayout`](xref:Xamarin.Forms.StackLayout) sont maintenant alignées horizontalement, au lieu de verticalement.
