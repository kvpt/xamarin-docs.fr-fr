---
ms.openlocfilehash: 48af50d31013f696879174a5cf108ab9fde92d0b
ms.sourcegitcommit: 3f0e4f10e5def19122588bb05f26ab2baa9df6eb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2020
ms.locfileid: "61343410"
---
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Dans **MainPage.xaml**, modifiez la déclaration [`Entry`](xref:Xamarin.Forms.Entry) pour personnaliser son comportement :

    ```xaml
    <Entry Placeholder="Enter password"
           MaxLength="15"
           IsSpellCheckEnabled="false"
           IsTextPredictionEnabled="false"
           IsPassword="true" />
    ```

    Ce code définit les propriétés qui personnalisent le comportement de [`Entry`](xref:Xamarin.Forms.Entry). La propriété [`MaxLength`](xref:Xamarin.Forms.InputView.MaxLength) limite la longueur d’entrée autorisée pour `Entry`, et la propriété [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) est définie sur `false` pour désactiver la vérification orthographique. De même, la propriété [`IsTextPredictionEnabled`](xref:Xamarin.Forms.Entry.IsTextPredictionEnabled) est définie sur `false` pour désactiver la prédiction de texte et la prédiction de texte automatique. Par ailleurs, la propriété [`IsPassword`](xref:Xamarin.Forms.Entry.IsPassword) permet de s’assurer que les caractères saisis seront masqués par un caractère de mot de passe (un cercle noir).

    > [!NOTE]
    > Dans certains scénarios de saisie de texte (saisie d’un mot de passe, par exemple), la vérification orthographique et la prédiction de texte produisent une expérience négative et doivent donc être désactivées.

1. Dans la barre d’outils Visual Studio, appuyez sur le bouton **Démarrer** (le bouton triangulaire qui ressemble à un bouton de lecture) pour lancer l’application à l’intérieur du simulateur iOS distant ou de l’émulateur Android de votre choix. Entrez du texte dans [`Entry`](xref:Xamarin.Forms.Entry). Vous remarquerez que chaque caractère est remplacé par un caractère de masque de mot de passe et que 15 caractères maximum peuvent être saisis :

    [![Capture d’écran d’une entrée constituée de texte masqué par des caractères de mot de passe, sur iOS et Android](../images/customize-behavior.png "Entrée constituée de texte masqué par des caractères de mot de passe")](../images/customize-behavior-large.png#lightbox "Entrée constituée de texte masqué par des caractères de mot de passe")

    Pour plus d’informations sur la personnalisation du comportement de [`Entry`](xref:Xamarin.Forms.Entry), consultez le guide [Xamarin.Forms Entry](~/xamarin-forms/user-interface/text/entry.md).

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio pour Mac](#tab/vsmac)

1. Dans **MainPage.xaml**, modifiez la déclaration [`Entry`](xref:Xamarin.Forms.Entry) pour personnaliser son comportement :

    ```xaml
    <Entry Placeholder="Enter password"
           MaxLength="15"
           IsSpellCheckEnabled="false"
           IsTextPredictionEnabled="false"
           IsPassword="true" />
    ```

    Ce code définit les propriétés qui personnalisent le comportement de [`Entry`](xref:Xamarin.Forms.Entry). La propriété [`MaxLength`](xref:Xamarin.Forms.InputView.MaxLength) limite la longueur d’entrée autorisée pour `Entry`, et la propriété [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) est définie sur `false` pour désactiver la vérification orthographique. De même, la propriété [`IsTextPredictionEnabled`](xref:Xamarin.Forms.Entry.IsTextPredictionEnabled) est définie sur `false` pour désactiver la prédiction de texte et la prédiction de texte automatique. Par ailleurs, la propriété [`IsPassword`](xref:Xamarin.Forms.Entry.IsPassword) permet de s’assurer que les caractères saisis seront masqués par un caractère de mot de passe (un cercle noir).

    > [!NOTE]
    > Dans certains scénarios de saisie de texte (saisie d’un mot de passe, par exemple), la vérification orthographique et la prédiction de texte produisent une expérience négative et doivent donc être désactivées.

1. Dans la barre d’outils Visual Studio pour Mac, appuyez sur le bouton **Démarrer** (le bouton triangulaire qui ressemble à un bouton de lecture) pour lancer l’application dans le simulateur iOS ou dans l’émulateur Android de votre choix. Entrez du texte dans [`Entry`](xref:Xamarin.Forms.Entry). Vous remarquerez que chaque caractère est remplacé par un caractère de masque de mot de passe et que 15 caractères maximum peuvent être saisis :

    [![Capture d’écran d’une entrée constituée de texte masqué par des caractères de mot de passe, sur iOS et Android](../images/customize-behavior.png "Entrée constituée de texte masqué par des caractères de mot de passe")](../images/customize-behavior-large.png#lightbox "Entrée constituée de texte masqué par des caractères de mot de passe")

    Pour plus d’informations sur la personnalisation du comportement de [`Entry`](xref:Xamarin.Forms.Entry), consultez le guide [Xamarin.Forms Entry](~/xamarin-forms/user-interface/text/entry.md).
