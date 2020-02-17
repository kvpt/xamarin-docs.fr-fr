---
title: Xamarin.Forms DualScreenHelper
description: Ce guide explique comment utiliser Xamarin.Forms DualScreenHelper pour optimiser l’expérience dans votre application sur des appareils double écran tels que Surface Duo et Surface Neo.
ms.prod: xamarin
ms.assetid: 5aa184c2-5611-427d-85c7-1c56486c3e1b
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 02/08/2020
ms.openlocfilehash: b06e105560de635a21b42e8366bb47842372cf6a
ms.sourcegitcommit: 07941cf9704ff88cf4087de5ebdea623ff54edb1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77145543"
---
# <a name="xamarinforms-dualscreenhelper"></a>Xamarin.Forms DualScreenHelper
[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://github.com/xamarin/xamarin-forms-samples/UserInterface/DualScreenDemos)
_DualScreenHelper permet de détecter si un appareil prend en charge le mode Image dans l’image, puis d’ouvrir un ContentPage sous forme d’une fenêtre Image dans l’image. Si vous utilisez un appareil Neo, la page s’ouvre dans le WonderBar en mode composition._
## <a name="properties"></a>Propriétés
- `HasCompactModeSupport` : permet de vérifier si la plateforme prend en charge l’ouverture d’un ContentPage en CompactMode.
- `OpenCompactMode` : si la plateforme le prend en charge, ouvre un ContentPage en CompactMode.
## <a name="example-usage"></a>Exemple d’utilisation
```c#
async void OpenCompactWindowClicked(object sender, EventArgs e)
{
    if(!DualScreenHelper.HasCompactModeSupport())
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