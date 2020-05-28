---
title: Styles de périphérique dansXamarin.Forms
description: Xamarin.Formscomprend six styles dynamiques, appelés styles de périphérique, dans la classe Device. styles. Cet article explique comment utiliser les styles d’appareil dans une Xamarin.Forms application.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b835847fea39e1c2f968e7b81fb9d22f68ea461c
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84140099"
---
# <a name="device-styles-in-xamarinforms"></a>Styles de périphérique dansXamarin.Forms

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-dynamicstyles)

_Xamarin. Forms comprend six styles dynamiques, appelés « styles de périphérique », dans la classe Device. styles._

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

![](device-images/device-styles.png "Device Styles on Each Platform")

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

[![](device-images/minimum-size.png "Accessible Small Device Styles on Each Platform")](device-images/minimum-size-large.png#lightbox "Accessible Small Device Styles on Each Platform")

Les captures d’écran suivantes illustrent les styles de périphérique sur chaque plateforme, avec la taille de police accessible la plus grande :

![](device-images/maximum-size.png "Accessible Large Device Styles on Each Platform")

## <a name="related-links"></a>Liens connexes

- [Styles de texte](~/xamarin-forms/user-interface/text/styles.md)
- [Extensions de balisage XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Styles dynamiques (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-dynamicstyles)
- [Utilisation des styles (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [Device. styles](xref:Xamarin.Forms.Device.Styles)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
- [Style](xref:Xamarin.Forms.Style)
- [Setter](xref:Xamarin.Forms.Setter)
