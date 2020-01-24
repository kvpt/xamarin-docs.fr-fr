---
ms.openlocfilehash: 841dac9486097e27923ccfe582803b4ec50371cf
ms.sourcegitcommit: 3f0e4f10e5def19122588bb05f26ab2baa9df6eb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2020
ms.locfileid: "60896678"
---
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Dans **MainPage.xaml**, modifiez la déclaration [`Label`](xref:Xamarin.Forms.Label) pour présenter un texte qui utilise plusieurs formats, dans un seul `Label`.

    ```xaml
    <Label TextColor="Gray"
           FontSize="Medium">
        <Label.FormattedText>
            <FormattedString>
                <Span Text="This sentence contains " />
                <Span Text="words that are emphasized, "
                      FontAttributes="Italic" />
                <Span Text="and underlined."
                      TextDecorations="Underline" />
            </FormattedString>
        </Label.FormattedText>
    </Label>
    ```

    Ce code affiche du texte dans un seul [`Label`](xref:Xamarin.Forms.Label) utilisant plusieurs formats. Le texte du premier [`Span`](xref:Xamarin.Forms.Span) s’affiche à l’aide de la mise en forme définie sur le `Label`, tandis que le texte des deuxième et troisième instances `Span` s’affiche avec la mise en forme définie sur le `Label` et la mise en forme supplémentaire définie sur chaque `Span`.

    > [!NOTE]
    > La propriété [`FormattedText`](xref:Xamarin.Forms.Label.FormattedText) est du type [`FormattedString`](xref:Xamarin.Forms.FormattedString), qui comprend une ou plusieurs instances [`Span`](xref:Xamarin.Forms.Span).

1. Dans la barre d’outils Visual Studio, appuyez sur le bouton **Démarrer** (le bouton triangulaire qui ressemble à un bouton de lecture) pour lancer l’application à l’intérieur du simulateur iOS distant ou de l’émulateur Android de votre choix. Vous remarquerez que l’apparence de l’élément [`Label`](xref:Xamarin.Forms.Label) a changé :

    [![Capture d’écran d’une étiquette comprenant du texte mis en forme, sur iOS et Android](../images/label-formatted-text.png "Étiquette comprenant du texte mis en forme")](../images/label-formatted-text-large.png#lightbox "Étiquette comprenant du texte mis en forme")

    Pour plus d’informations sur la configuration de l’apparence de [`Span`](xref:Xamarin.Forms.Span), consultez la rubrique [Texte mis en forme](~/xamarin-forms/user-interface/text/label.md#formatted-text) du guide [Étiquette de Xamarin.Forms](~/xamarin-forms/user-interface/text/label.md).

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio pour Mac](#tab/vsmac)

1. Dans **MainPage.xaml**, modifiez la déclaration [`Label`](xref:Xamarin.Forms.Label) pour présenter un texte qui utilise plusieurs formats, dans un seul `Label`.

    ```xaml
    <Label TextColor="Gray"
           FontSize="Medium">
        <Label.FormattedText>
            <FormattedString>
                <Span Text="This sentence contains " />
                <Span Text="words that are emphasized, "
                      FontAttributes="Italic" />
                <Span Text="and underlined."
                      TextDecorations="Underline" />
            </FormattedString>
        </Label.FormattedText>
    </Label>
    ```

    Ce code affiche du texte dans un seul [`Label`](xref:Xamarin.Forms.Label) utilisant plusieurs formats. Le texte du premier [`Span`](xref:Xamarin.Forms.Span) s’affiche à l’aide de la mise en forme définie sur le `Label`, tandis que le texte des deuxième et troisième instances `Span` s’affiche avec la mise en forme définie sur le `Label` et la mise en forme supplémentaire définie sur chaque `Span`.

    > [!NOTE]
    > La propriété [`FormattedText`](xref:Xamarin.Forms.Label.FormattedText) est du type [`FormattedString`](xref:Xamarin.Forms.FormattedString), qui comprend une ou plusieurs instances [`Span`](xref:Xamarin.Forms.Span).

1. Dans la barre d’outils Visual Studio pour Mac, appuyez sur le bouton **Démarrer** (le bouton triangulaire qui ressemble à un bouton de lecture) pour lancer l’application dans le simulateur iOS ou dans l’émulateur Android de votre choix. Vous remarquerez que l’apparence de l’élément [`Label`](xref:Xamarin.Forms.Label) a changé :

    [![Capture d’écran d’une étiquette comprenant du texte mis en forme, sur iOS et Android](../images/label-formatted-text.png "Étiquette comprenant du texte mis en forme")](../images/label-formatted-text-large.png#lightbox "Étiquette comprenant du texte mis en forme")

    Pour plus d’informations sur la configuration de l’apparence de [`Span`](xref:Xamarin.Forms.Span), consultez la rubrique [Texte mis en forme](~/xamarin-forms/user-interface/text/label.md#formatted-text) du guide [Étiquette de Xamarin.Forms](~/xamarin-forms/user-interface/text/label.md).
