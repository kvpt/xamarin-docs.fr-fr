---
ms.openlocfilehash: 97e52d593e2b59d127da324d8ad74ad7b2096cdd
ms.sourcegitcommit: 83cf2a4d99546751c6394510a463a2b2a8bf75b8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83343417"
---
La taille et la position des vues enfants dans [`StackLayout`](xref:Xamarin.Forms.StackLayout) varient selon les valeurs des propriétés [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) et [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) des vues enfants et selon les valeurs des propriétés [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) et [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions).

Les propriétés [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) et [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) peuvent être définies sur des champs à partir de la struct [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions), qui encapsule deux préférences de mise en page :

- **Alignement** : les préférences d’alignement de la vue enfant, qui déterminent sa position et da taille au sein dans sa mise en page parente.
- **Extension** : indique si la vue enfant doit utiliser un espace supplémentaire, si elle est disponible (utilisé uniquement par [`StackLayout`](xref:Xamarin.Forms.StackLayout)).

# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

1. Dans **MainPage.xaml**, modifiez la déclaration [`StackLayout`](xref:Xamarin.Forms.StackLayout) pour définir les options d’alignement et d’extension pour chaque [`Label`](xref:Xamarin.Forms.Label) :

    ```xaml
    <StackLayout Margin="20,35,20,25">
        <Label Text="Start"
               HorizontalOptions="Start"
               BackgroundColor="Gray" />
        <Label Text="Center"
               HorizontalOptions="Center"
               BackgroundColor="Gray" />
        <Label Text="End"
               HorizontalOptions="End"
               BackgroundColor="Gray" />
        <Label Text="Fill"
               HorizontalOptions="Fill"
               BackgroundColor="Gray" />
        <Label Text="StartAndExpand"
               VerticalOptions="StartAndExpand"
               BackgroundColor="Gray" />
        <Label Text="CenterAndExpand"
               VerticalOptions="CenterAndExpand"
               BackgroundColor="Gray" />
        <Label Text="EndAndExpand"
               VerticalOptions="EndAndExpand"
               BackgroundColor="Gray" />
        <Label Text="FillAndExpand"
               VerticalOptions="FillAndExpand"
               BackgroundColor="Gray" />
    </StackLayout>
    ```

    Ce code définit les préférences d’alignement sur les quatre premières instances [`Label`](xref:Xamarin.Forms.Label) ainsi que les préférences d’extension sur les quatre dernières instances `Label`. Les champs [`Start`](xref:Xamarin.Forms.LayoutOptions.Start), [`Center`](xref:Xamarin.Forms.LayoutOptions.Center), [`End`](xref:Xamarin.Forms.LayoutOptions.End) et [`Fill`](xref:Xamarin.Forms.LayoutOptions.Fill) sont utilisés pour définir l’alignement des instances [`Label`](xref:Xamarin.Forms.Label) dans le [`StackLayout`](xref:Xamarin.Forms.StackLayout) parent. Les champs [`StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand), [`CenterAndExpand`](xref:Xamarin.Forms.LayoutOptions.CenterAndExpand), [`EndAndExpand`](xref:Xamarin.Forms.LayoutOptions.EndAndExpand) et [`FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand) sont utilisés pour définir les préférences d’alignement et indiquent si la vue occupe plus d’espace que la quantité disponible dans le `StackLayout` parent.

    > [!NOTE]
    > La valeur par défaut des propriétés [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) et [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) d’une vue est [`Fill`](xref:Xamarin.Forms.LayoutOptions.Fill).

1. Dans la barre d’outils Visual Studio, appuyez sur le bouton **Démarrer** (le bouton triangulaire qui ressemble à un bouton de lecture) pour lancer l’application à l’intérieur du simulateur iOS distant ou de l’émulateur Android de votre choix :

    [![Capture d’écran de vues enfants dans une disposition de pile, avec des options d’alignement et d’expansion définies, sur iOS et Android](../images/alignment-expansion.png "Disposition de pile comprenant des instances d’étiquettes, avec des options d’alignement et d’expansion définies")](../images/alignment-expansion-large.png#lightbox "Disposition de pile comprenant des instances d’étiquettes, avec des options d’alignement et d’expansion définies")

    Un [`StackLayout`](xref:Xamarin.Forms.StackLayout) respecte uniquement les préférences d’alignement sur les vues enfants qui se trouvent dans la direction opposée à l’orientation `StackLayout`. Par conséquent, les vues enfants [`Label`](xref:Xamarin.Forms.Label) dans `StackLayout` orienté verticalement définissent leurs propriétés [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) sur l’un des champs d’alignement :

    - [`Start`](xref:Xamarin.Forms.LayoutOptions.Start), qui positionne [`Label`](xref:Xamarin.Forms.Label) à gauche de [`StackLayout`](xref:Xamarin.Forms.StackLayout).
    - [`Center`](xref:Xamarin.Forms.LayoutOptions.Center), qui centre [`Label`](xref:Xamarin.Forms.Label) dans [`StackLayout`](xref:Xamarin.Forms.StackLayout).
    - [`End`](xref:Xamarin.Forms.LayoutOptions.End), qui positionne [`Label`](xref:Xamarin.Forms.Label) à droite de [`StackLayout`](xref:Xamarin.Forms.StackLayout).
    - [`Fill`](xref:Xamarin.Forms.LayoutOptions.Fill), qui garantit que [`Label`](xref:Xamarin.Forms.Label) remplit la largeur de [`StackLayout`](xref:Xamarin.Forms.StackLayout).

    Un [`StackLayout`](xref:Xamarin.Forms.StackLayout) peut uniquement développer les vues enfants dans le sens de son orientation. Par conséquent, le `StackLayout` orienté verticalement peut développer les vues enfants [`Label`](xref:Xamarin.Forms.Label) qui définissent leurs propriétés [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) sur l’un des champs d’extension. Cela signifie que, pour l’alignement vertical, chaque `Label` occupe la même quantité d’espace dans `StackLayout`. En revanche, seule la dernière instance `Label`, qui définit sa propriété [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) sur [`FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand) a une taille différente.

    > [!IMPORTANT]
    > Lorsque tout l’espace de [`StackLayout`](xref:Xamarin.Forms.StackLayout) est utilisé, les préférences d’extension n’ont aucun effet.

    Pour plus d’informations sur l’alignement et l’extension, consultez [Options de mise en page dans Xamarin.Forms](~/xamarin-forms/user-interface/layouts/layout-options.md).

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/vsmac)

1. Dans **MainPage.xaml**, modifiez la déclaration [`StackLayout`](xref:Xamarin.Forms.StackLayout) pour définir les options d’alignement et d’extension pour chaque [`Label`](xref:Xamarin.Forms.Label) :

    ```xaml
    <StackLayout Margin="20,35,20,25">
        <Label Text="Start"
               HorizontalOptions="Start"
               BackgroundColor="Gray" />
        <Label Text="Center"
               HorizontalOptions="Center"
               BackgroundColor="Gray" />
        <Label Text="End"
               HorizontalOptions="End"
               BackgroundColor="Gray" />
        <Label Text="Fill"
               HorizontalOptions="Fill"
               BackgroundColor="Gray" />
        <Label Text="StartAndExpand"
               VerticalOptions="StartAndExpand"
               BackgroundColor="Gray" />
        <Label Text="CenterAndExpand"
               VerticalOptions="CenterAndExpand"
               BackgroundColor="Gray" />
        <Label Text="EndAndExpand"
               VerticalOptions="EndAndExpand"
               BackgroundColor="Gray" />
        <Label Text="FillAndExpand"
               VerticalOptions="FillAndExpand"
               BackgroundColor="Gray" />
    </StackLayout>
    ```

    Ce code définit les préférences d’alignement sur les quatre premières instances [`Label`](xref:Xamarin.Forms.Label) ainsi que les préférences d’extension sur les quatre dernières instances `Label`. Les champs [`Start`](xref:Xamarin.Forms.LayoutOptions.Start), [`Center`](xref:Xamarin.Forms.LayoutOptions.Center), [`End`](xref:Xamarin.Forms.LayoutOptions.End) et [`Fill`](xref:Xamarin.Forms.LayoutOptions.Fill) sont utilisés pour définir l’alignement des instances [`Label`](xref:Xamarin.Forms.Label) dans le [`StackLayout`](xref:Xamarin.Forms.StackLayout) parent. Les champs [`StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand), [`CenterAndExpand`](xref:Xamarin.Forms.LayoutOptions.CenterAndExpand), [`EndAndExpand`](xref:Xamarin.Forms.LayoutOptions.EndAndExpand) et [`FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand) sont utilisés pour définir les préférences d’alignement et indiquent si la vue occupe plus d’espace que la quantité disponible dans le `StackLayout` parent.

    > [!NOTE]
    > La valeur par défaut des propriétés [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) et [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) d’une vue est [`Fill`](xref:Xamarin.Forms.LayoutOptions.Fill).

1. Dans la barre d’outils Visual Studio pour Mac, appuyez sur le bouton **Démarrer** (le bouton triangulaire qui ressemble à un bouton de lecture) pour lancer l’application dans le simulateur iOS ou dans l’émulateur Android de votre choix :

    [![Capture d’écran de vues enfants dans une disposition de pile, avec des options d’alignement et d’expansion définies, sur iOS et Android](../images/alignment-expansion.png "Disposition de pile comprenant des instances d’étiquettes, avec des options d’alignement et d’expansion définies")](../images/alignment-expansion-large.png#lightbox "Disposition de pile comprenant des instances d’étiquettes, avec des options d’alignement et d’expansion définies")

    Un [`StackLayout`](xref:Xamarin.Forms.StackLayout) respecte uniquement les préférences d’alignement sur les vues enfants qui se trouvent dans la direction opposée à l’orientation `StackLayout`. Par conséquent, les vues enfants [`Label`](xref:Xamarin.Forms.Label) dans `StackLayout` orienté verticalement définissent leurs propriétés [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) sur l’un des champs d’alignement :

    - [`Start`](xref:Xamarin.Forms.LayoutOptions.Start), qui positionne [`Label`](xref:Xamarin.Forms.Label) à gauche de [`StackLayout`](xref:Xamarin.Forms.StackLayout).
    - [`Center`](xref:Xamarin.Forms.LayoutOptions.Center), qui centre [`Label`](xref:Xamarin.Forms.Label) dans [`StackLayout`](xref:Xamarin.Forms.StackLayout).
    - [`End`](xref:Xamarin.Forms.LayoutOptions.End), qui positionne [`Label`](xref:Xamarin.Forms.Label) à droite de [`StackLayout`](xref:Xamarin.Forms.StackLayout).
    - [`Fill`](xref:Xamarin.Forms.LayoutOptions.Fill), qui garantit que [`Label`](xref:Xamarin.Forms.Label) remplit la largeur de [`StackLayout`](xref:Xamarin.Forms.StackLayout).

    Un [`StackLayout`](xref:Xamarin.Forms.StackLayout) peut uniquement développer les vues enfants dans le sens de son orientation. Par conséquent, le `StackLayout` orienté verticalement peut développer les vues enfants [`Label`](xref:Xamarin.Forms.Label) qui définissent leurs propriétés [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) sur l’un des champs d’extension. Cela signifie que, pour l’alignement vertical, chaque `Label` occupe la même quantité d’espace dans `StackLayout`. En revanche, seule la dernière instance `Label`, qui définit sa propriété [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) sur [`FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand) a une taille différente.

    > [!IMPORTANT]
    > Lorsque tout l’espace de [`StackLayout`](xref:Xamarin.Forms.StackLayout) est utilisé, les préférences d’extension n’ont aucun effet.

    Pour plus d’informations sur l’alignement et l’extension, consultez [Options de mise en page dans Xamarin.Forms](~/xamarin-forms/user-interface/layouts/layout-options.md).
