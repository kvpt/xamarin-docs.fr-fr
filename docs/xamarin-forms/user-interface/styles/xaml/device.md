---
title: Styles de périphérique dans Xamarin.Forms
description: Xamarin.Forms comprend six styles dynamiques, appelés styles de périphérique, dans la classe Device. styles. Cet article explique comment utiliser les styles d’appareil dans une Xamarin.Forms application.
ms.prod: xamarin
ms.assetid: 7FF19ED1-0822-4238-9435-AD970317A2F8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b8665e5489da396cb9f7ef984c97fe79fda17434
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91558321"
---
# <a name="device-styles-in-no-locxamarinforms"></a>Styles de périphérique dans Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-dynamicstyles)

_Xamarin.Forms comprend six styles dynamiques, appelés styles de périphérique, dans la classe Device. styles._

Les styles d' *appareil* sont les suivants :

- [`BodyStyle`](xref:Xamarin.Forms.Device.Styles.BodyStyle)
- [`CaptionStyle`](xref:Xamarin.Forms.Device.Styles.CaptionStyle)
- [`ListItemDetailTextStyle`](xref:Xamarin.Forms.Device.Styles.ListItemDetailTextStyle)
- [`ListItemTextStyle`](xref:Xamarin.Forms.Device.Styles.ListItemTextStyle)
- [`SubtitleStyle`](xref:Xamarin.Forms.Device.Styles.SubtitleStyle)
- [`TitleStyle`](xref:Xamarin.Forms.Device.Styles.TitleStyle)

Les six styles ne peuvent être appliqués qu’à des [`Label`](xref:Xamarin.Forms.Label) instances. Par exemple, un `Label` qui affiche le corps d’un paragraphe peut affecter à sa [`Style`](xref:Xamarin.Forms.NavigableElement.Style) propriété la valeur [`BodyStyle`](xref:Xamarin.Forms.Device.Styles.BodyStyle) .

L’exemple de code suivant montre comment utiliser les styles d' *appareil* dans une page XAML :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.DeviceStylesPage" Title="Device" IconImageSource="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="myBodyStyle" TargetType="Label"
              BaseResourceKey="BodyStyle">
                <Setter Property="TextColor" Value="Accent" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <Label Text="Title style"
              Style="{DynamicResource TitleStyle}" />
            <Label Text="Subtitle text style"
              Style="{DynamicResource SubtitleStyle}" />
            <Label Text="Body style"
              Style="{DynamicResource BodyStyle}" />
            <Label Text="Caption style"
              Style="{DynamicResource CaptionStyle}" />
            <Label Text="List item detail text style"
              Style="{DynamicResource ListItemDetailTextStyle}" />
            <Label Text="List item text style"
              Style="{DynamicResource ListItemTextStyle}" />
            <Label Text="No style" />
            <Label Text="My body style"
              Style="{StaticResource myBodyStyle}" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

Les styles d’appareil sont liés à à l’aide de l' `DynamicResource` extension de balisage. La nature dynamique des styles peut être vue dans iOS en modifiant les paramètres d' **accessibilité** pour la taille du texte. L’apparence des styles d' *appareil* est différente sur chaque plateforme, comme illustré dans les captures d’écran suivantes :

![Styles de périphériques sur chaque plateforme](device-images/device-styles.png)

Les styles d' *appareil* peuvent également être dérivés de en affectant [`BaseResourceKey`](xref:Xamarin.Forms.Style.BaseResourceKey) à la propriété le nom de clé du style d’appareil. Dans l’exemple de code ci-dessus, `myBodyStyle` hérite de [`BodyStyle`](xref:Xamarin.Forms.Device.Styles.BodyStyle) et définit une couleur de texte accentuée. Pour plus d’informations sur l’héritage de style dynamique, consultez [héritage de style dynamique](~/xamarin-forms/user-interface/styles/xaml/dynamic.md#dynamic-style-inheritance).

L’exemple de code suivant illustre la page équivalente en C# :

```csharp
public class DeviceStylesPageCS : ContentPage
{
    public DeviceStylesPageCS ()
    {
        var myBodyStyle = new Style (typeof(Label)) {
            BaseResourceKey = Device.Styles.BodyStyleKey,
            Setters = {
                new Setter {
                    Property = Label.TextColorProperty,
                    Value = Color.Accent
                }
            }
        };

        Title = "Device";
        IconImageSource = "csharp.png";
        Padding = new Thickness (0, 20, 0, 0);

        Content = new StackLayout {
            Children = {
                new Label { Text = "Title style", Style = Device.Styles.TitleStyle },
                new Label { Text = "Subtitle style", Style = Device.Styles.SubtitleStyle },
                new Label { Text = "Body style", Style = Device.Styles.BodyStyle },
                new Label { Text = "Caption style", Style = Device.Styles.CaptionStyle },
                new Label { Text = "List item detail text style",
                  Style = Device.Styles.ListItemDetailTextStyle },
                new Label { Text = "List item text style", Style = Device.Styles.ListItemTextStyle },
                new Label { Text = "No style" },
                new Label { Text = "My body style", Style = myBodyStyle }
            }
        };
    }
}
```

La [`Style`](xref:Xamarin.Forms.NavigableElement.Style) propriété de chaque [`Label`](xref:Xamarin.Forms.Label) instance est définie sur la propriété appropriée à partir de la [`Devices.Styles`](xref:Xamarin.Forms.Device.Styles) classe.

## <a name="accessibility"></a>Accessibilité

Les styles de *périphérique* respectent les préférences d’accessibilité, de sorte que les tailles de police varient en fonction des modifications apportées aux préférences d’accessibilité sur chaque plateforme. Par conséquent, pour prendre en charge le texte accessible, assurez-vous que les styles de *périphérique* sont utilisés comme base pour tous les styles de texte dans votre application.

Les captures d’écran suivantes montrent les styles de périphérique sur chaque plateforme, avec la plus petite taille de police accessible :

[![Petits styles d’appareils accessibles sur chaque plateforme](device-images/minimum-size.png)](device-images/minimum-size-large.png#lightbox "Petits styles d’appareils accessibles sur chaque plateforme")

Les captures d’écran suivantes illustrent les styles de périphérique sur chaque plateforme, avec la taille de police accessible la plus grande :

![Accessibilité des grands styles d’appareils sur chaque plateforme](device-images/maximum-size.png)

## <a name="related-links"></a>Liens connexes

- [Styles de texte](~/xamarin-forms/user-interface/text/styles.md)
- [Extensions de balisage XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Styles dynamiques (exemple)](/samples/xamarin/xamarin-forms-samples/userinterface-styles-dynamicstyles)
- [Utilisation des styles (exemple)](/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [Device. styles](xref:Xamarin.Forms.Device.Styles)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
- [Style](xref:Xamarin.Forms.Style)
- [Setter](xref:Xamarin.Forms.Setter)