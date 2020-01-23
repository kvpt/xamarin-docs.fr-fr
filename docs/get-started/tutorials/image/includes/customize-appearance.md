---
ms.openlocfilehash: d8f9445a0fc45c2700e8d9a901cfce9bd6307d2b
ms.sourcegitcommit: 3f0e4f10e5def19122588bb05f26ab2baa9df6eb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2020
ms.locfileid: "75490682"
---
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Dans **MainPage.xaml**, modifiez la déclaration [`Image`](xref:Xamarin.Forms.Image) pour personnaliser son apparence :

    ```xaml
    <Image Source="http://upload.wikimedia.org/wikipedia/commons/thumb/f/fc/Papio_anubis_%28Serengeti%2C_2009%29.jpg/200px-Papio_anubis_%28Serengeti%2C_2009%29.jpg"
           Aspect="Fill"
           HeightRequest="{OnPlatform iOS=300, Android=250}"
           WidthRequest="{OnPlatform iOS=300, Android=250}"
           HorizontalOptions="Center" />
    ```

    Ce code définit la propriété [`Aspect`](xref:Xamarin.Forms.Image.Aspect) (qui spécifie le mode de mise à l’échelle de l’image) sur [`Fill`](xref:Xamarin.Forms.Aspect.Fill). Le membre `Fill` est défini dans l’énumération [`Aspect`](xref:Xamarin.Forms.Aspect) et étire l’image pour remplir entièrement la vue, que l’image soit déformée ou non. Pour plus d’informations sur la mise à l’échelle d’images, consultez la rubrique [Affichage des images](~/xamarin-forms/user-interface/images.md#display-images) du guide [Images dans Xamarin.Forms](~/xamarin-forms/user-interface/images.md).

    L’extension de balisage `OnPlatform` vous permet de personnaliser l’apparence de l’interface utilisateur pour chaque plateforme. Dans cet exemple, l’extension de balisage est utilisée pour définir les propriétés [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) et [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) sur 300 unités indépendantes du périphérique sur iOS et sur 250 unités indépendantes du périphérique sur Android. Pour plus d’informations sur l’extension de balisage `OnPlatform`, consultez la rubrique [Extension de balisage OnPlatform](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform) du guide [Utilisation des Extensions de balisage XAML](~/xamarin-forms/xaml/markup-extensions/consuming.md).

    En outre, la propriété [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) indique que l’image est centrée horizontalement.

1. Dans la barre d’outils Visual Studio, appuyez sur le bouton **Démarrer** (le bouton triangulaire qui ressemble à un bouton de lecture) pour lancer l’application à l’intérieur du simulateur iOS distant ou de l’émulateur Android de votre choix :

    [![Capture d’écran d’une image dont la taille est différente sur iOS et Android](../images/customize-appearance.png "Image dimensionnée en fonction de la plateforme")](../images/customize-appearance-large.png#lightbox "Image dimensionnée en fonction de la plateforme")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio pour Mac](#tab/vsmac)

1. Dans **MainPage.xaml**, modifiez la déclaration [`Image`](xref:Xamarin.Forms.Image) pour personnaliser son apparence :

    ```xaml
    <Image Source="http://upload.wikimedia.org/wikipedia/commons/thumb/f/fc/Papio_anubis_%28Serengeti%2C_2009%29.jpg/200px-Papio_anubis_%28Serengeti%2C_2009%29.jpg"
           Aspect="Fill"
           HeightRequest="{OnPlatform iOS=300, Android=250}"
           WidthRequest="{OnPlatform iOS=300, Android=250}"
           HorizontalOptions="Center" />
    ```

    Ce code définit la propriété [`Aspect`](xref:Xamarin.Forms.Image.Aspect) (qui spécifie le mode de mise à l’échelle de l’image) sur [`Fill`](xref:Xamarin.Forms.Aspect.Fill). Le membre `Fill` est défini dans l’énumération [`Aspect`](xref:Xamarin.Forms.Aspect) et étire l’image pour remplir entièrement la vue, que l’image soit déformée ou non. Pour plus d’informations sur la mise à l’échelle d’images, consultez la rubrique [Affichage des images](~/xamarin-forms/user-interface/images.md#display-images) du guide [Images dans Xamarin.Forms](~/xamarin-forms/user-interface/images.md).

    L’extension de balisage `OnPlatform` vous permet de personnaliser l’apparence de l’interface utilisateur pour chaque plateforme. Dans cet exemple, l’extension de balisage est utilisée pour définir les propriétés [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) et [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) sur 300 sous iOS et sur 250 sous Android. Pour plus d’informations sur l’extension de balisage `OnPlatform`, consultez la rubrique [Extension de balisage OnPlatform](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform) du guide [Utilisation des Extensions de balisage XAML](~/xamarin-forms/xaml/markup-extensions/consuming.md).

    En outre, la propriété [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) indique que l’image est centrée horizontalement.

1. Dans la barre d’outils Visual Studio pour Mac, appuyez sur le bouton **Démarrer** (le bouton triangulaire qui ressemble à un bouton de lecture) pour lancer l’application dans le simulateur iOS ou dans l’émulateur Android de votre choix :

    [![Capture d’écran d’une image dont la taille est différente sur iOS et Android](../images/customize-appearance.png "Image dimensionnée en fonction de la plateforme")](../images/customize-appearance-large.png#lightbox "Image dimensionnée en fonction de la plateforme")
