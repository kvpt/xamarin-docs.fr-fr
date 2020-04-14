---
ms.openlocfilehash: bd3f37082443e93f10f60d9466fe22aae8571b14
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/09/2020
ms.locfileid: "61373373"
---
# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

1. Dans **MainPage.xaml**, modifiez la déclaration [`Editor`](xref:Xamarin.Forms.Editor) pour personnaliser son comportement :

    ```xaml
    <Editor Placeholder="Enter multi-line text here"
            AutoSize="TextChanges"
            MaxLength="200"
            IsSpellCheckEnabled="false"
            IsTextPredictionEnabled="false" />
    ```

    Ce code définit les propriétés qui personnalisent le comportement de [`Editor`](xref:Xamarin.Forms.Editor). La propriété [`AutoSize`](xref:Xamarin.Forms.Editor.AutoSize) est définie sur [`TextChanges`](xref:Xamarin.Forms.EditorAutoSizeOption.TextChanges), ce qui indique que la hauteur de l’élément `Editor` augmente lorsqu’il comporte du texte et diminue lorsqu’il est vide. La propriété [`MaxLength`](xref:Xamarin.Forms.InputView.MaxLength) limite la longueur de l’entrée autorisée pour l’élément `Editor`. En outre, la propriété [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) est définie sur `false` pour désactiver la vérification orthographique, tandis que la propriété `IsTextPredictionEnabled` est définie sur `false` pour désactiver la prédiction de texte (automatique ou non).

1. Dans la barre d’outils Visual Studio, appuyez sur le bouton **Démarrer** (le bouton triangulaire qui ressemble à un bouton de lecture) pour lancer l’application à l’intérieur du simulateur iOS distant ou de l’émulateur Android de votre choix. Entrez le texte dans [`Editor`](xref:Xamarin.Forms.Entry), et constatez que la hauteur de l’élément `Editor` augmente lorsqu’il contient du texte et diminue à mesure que le texte est supprimé. Le nombre maximal de caractères est de 200 :

    [![Capture d’écran d’un éditeur à redimensionnement automatique, sur iOS et Android](../images/customize-behavior.png "Éditeur à redimensionnement automatique")](../images/customize-behavior-large.png#lightbox "Éditeur à redimensionnement automatique")

    Pour plus d’informations sur la personnalisation du comportement de [`Editor`](xref:Xamarin.Forms.Editor), consultez le guide [Xamarin.Forms Editor](~/xamarin-forms/user-interface/text/editor.md).

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/vsmac)

1. Dans **MainPage.xaml**, modifiez la déclaration [`Editor`](xref:Xamarin.Forms.Editor) pour personnaliser son comportement :

    ```xaml
    <Editor Placeholder="Enter multi-line text here"
            AutoSize="TextChanges"
            MaxLength="200"
            IsSpellCheckEnabled="false"
            IsTextPredictionEnabled="false" />
    ```

    Ce code définit les propriétés qui personnalisent le comportement de [`Editor`](xref:Xamarin.Forms.Editor). La propriété [`AutoSize`](xref:Xamarin.Forms.Editor.AutoSize) est définie sur [`TextChanges`](xref:Xamarin.Forms.EditorAutoSizeOption.TextChanges), ce qui indique que la hauteur de l’élément `Editor` augmente lorsqu’il comporte du texte et diminue lorsqu’il est vide. La propriété [`MaxLength`](xref:Xamarin.Forms.InputView.MaxLength) limite la longueur de l’entrée autorisée pour l’élément `Editor`. En outre, la propriété [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) est définie sur `false` pour désactiver la vérification orthographique, tandis que la propriété `IsTextPredictionEnabled` est définie sur `false` pour désactiver la prédiction de texte (automatique ou non).

1. Dans la barre d’outils Visual Studio pour Mac, appuyez sur le bouton **Démarrer** (le bouton triangulaire qui ressemble à un bouton de lecture) pour lancer l’application dans le simulateur iOS ou dans l’émulateur Android de votre choix. Entrez le texte dans [`Editor`](xref:Xamarin.Forms.Entry), et constatez que la hauteur de l’élément `Editor` augmente lorsqu’il contient du texte et diminue à mesure que le texte est supprimé. Le nombre maximal de caractères est de 200 :

    [![Capture d’écran d’un éditeur à redimensionnement automatique, sur iOS et Android](../images/customize-behavior.png "Éditeur à redimensionnement automatique")](../images/customize-behavior-large.png#lightbox "Éditeur à redimensionnement automatique")

    Pour plus d’informations sur la personnalisation du comportement de [`Editor`](xref:Xamarin.Forms.Editor), consultez le guide [Xamarin.Forms Editor](~/xamarin-forms/user-interface/text/editor.md).
