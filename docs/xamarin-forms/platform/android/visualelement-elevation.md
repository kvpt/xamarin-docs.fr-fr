---
title: Élévation VisualElement sur Android
description: Les spécificités des plateformes vous permettent d’utiliser des fonctionnalités uniquement disponibles sur une plateforme spécifique, sans implémenter de convertisseurs ou d’effets personnalisés. Cet article explique comment utiliser le spécifique à la plateforme Android qui contrôle l’élévation de VisualElements sur les applications qui ciblent l’API 21 ou une version ultérieure.
ms.prod: xamarin
ms.assetid: 5BFD6175-2BBD-41CD-B8F9-521B4750B708
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 19f09025e44bb7deddbb8a9e6ae326d2137a5ce0
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93367528"
---
# <a name="visualelement-elevation-on-android"></a>Élévation VisualElement sur Android

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Ce spécifique à la plateforme Android est utilisé pour contrôler l’élévation, ou ordre de plan, des éléments visuels sur les applications qui ciblent l’API 21 ou une version ultérieure. L’élévation d’un élément visuel détermine son ordre de dessin, avec des éléments visuels avec des valeurs Z plus élevées obturant des éléments visuels avec des valeurs Z inférieures. Il est consommé en XAML en affectant `VisualElement.Elevation` une valeur à la propriété jointe `boolean` :

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
             Title="Elevation">
    <StackLayout>
        <Grid>
            <Button Text="Button Beneath BoxView" />
            <BoxView Color="Red" Opacity="0.2" HeightRequest="50" />
        </Grid>        
        <Grid Margin="0,20,0,0">
            <Button Text="Button Above BoxView - Click Me" android:VisualElement.Elevation="10"/>
            <BoxView Color="Red" Opacity="0.2" HeightRequest="50" />
        </Grid>
    </StackLayout>
</ContentPage>
```

Elle peut également être utilisée à partir de C# à l’aide de l’API Fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

public class AndroidElevationPageCS : ContentPage
{
    public AndroidElevationPageCS()
    {
        ...
        var aboveButton = new Button { Text = "Button Above BoxView - Click Me" };
        aboveButton.On<Android>().SetElevation(10);

        Content = new StackLayout
        {
            Children =
            {
                new Grid
                {
                    Children =
                    {
                        new Button { Text = "Button Beneath BoxView" },
                        new BoxView { Color = Color.Red, Opacity = 0.2, HeightRequest = 50 }
                    }
                },
                new Grid
                {
                    Margin = new Thickness(0,20,0,0),
                    Children =
                    {
                        aboveButton,
                        new BoxView { Color = Color.Red, Opacity = 0.2, HeightRequest = 50 }
                    }
                }
            }
        };
    }
}
```

La `Button.On<Android>` méthode spécifie que ce spécifique à la plateforme s’exécutera uniquement sur Android. La `VisualElement.SetElevation` méthode, dans l' [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) espace de noms, est utilisée pour affecter une valeur null à l’élévation de l’élément visuel `float` . En outre, la `VisualElement.GetElevation` méthode peut être utilisée pour récupérer la valeur d’élévation d’un élément visuel.

Le résultat est que l’élévation d’éléments visuels peut être contrôlée afin que les éléments visuels avec des valeurs Z supérieures occultait les éléments visuels avec des valeurs Z inférieures. Par conséquent, dans cet exemple, le deuxième [`Button`](xref:Xamarin.Forms.Button) est rendu au-dessus de [`BoxView`](xref:Xamarin.Forms.BoxView) , car il a une valeur d’élévation supérieure :

![Capture d’écran d’élévation VisualElement](visualelement-elevation-images/elevation.png)

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [API AndroidSpecific. AppCompat](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)