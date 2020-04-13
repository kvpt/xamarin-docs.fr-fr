---
title: Xamarin.Forms assistant plate-forme à double écran
description: Ce guide explique comment utiliser la classe DualScreenHelper de Xamarin.Forms pour optimiser l’expérience dans votre application sur des appareils double écran, comme Surface Duo et Surface Neo.
ms.prod: xamarin
ms.assetid: 5aa184c2-5611-427d-85c7-1c56486c3e1b
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 02/08/2020
ms.openlocfilehash: 9ab6106b8b92660d416e8a22cc3b1bdf1b41c5cf
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "80628275"
---
# <a name="xamarinforms-dual-screen-platform-helpers"></a>Xamarin.Forms assistant plate-forme à double écran

![](~/media/shared/preview.png "This API is currently pre-release")

[![Télécharger](~/media/shared/download.png) l’échantillon Télécharger l’échantillon](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-dualscreendemos/)

La classe `DualScreenHelper` peut être utilisée pour détecter si un appareil prend en charge le mode Image dans l’image, puis pour ouvrir une `ContentPage` sous la forme d’une fenêtre Image dans l’image. Si vous utilisez un appareil Neo, elle ouvre la page dans le WonderBar en mode composition.

## <a name="properties"></a>Propriétés

- `HasCompactModeSupport` est utilisée pour vérifier si la plateforme prend en charge l’ouverture d’une `ContentPage` en CompactMode.
- `OpenCompactMode` ouvre une `ContentPage` en CompactMode, s’il est pris en charge par la plateforme.

## <a name="example"></a>Exemple

```csharp
async void OpenCompactWindowClicked(object sender, EventArgs e)
{
    if (!DualScreenHelper.HasCompactModeSupport())
    {
        await DisplayAlert("Unsupported", "This platform doesn't support this feature", "Ok");
        return;
    }

    ContentPage page = new ContentPage() { BackgroundColor = Color.Purple };

    var button = new Button()
    {
        Text = "Close this Window"
    };

    var layout = new StackLayout()
    {
        Children =
        {
            new Label(){ Text = "Welcome to your Compact Mode Window" },
            button
        },
        BackgroundColor = Color.Yellow,
        HorizontalOptions = LayoutOptions.Fill,
        VerticalOptions = LayoutOptions.Fill
    };

    page.Content = new ScrollView() { Content = layout };
    var args = await DualScreenHelper.OpenCompactMode(page);

    button.Command = new Command(async () =>
    {
        await args.CloseAsync();
    });
}
```
