---
title: Styles d’appareils dans Xamarin.Forms
description: Xamarin.Forms inclut six styles dynamiques, connus comme des styles de périphérique, dans la classe Device.Styles. Cet article explique comment utiliser les styles de périphérique dans une application Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 7FF19ED1-0822-4238-9435-AD970317A2F8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
ms.openlocfilehash: 4131844d49d7fdad4c97d07fb699b96db2020ec4
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68647295"
---
# <a name="device-styles-in-xamarinforms"></a>Styles d’appareils dans Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-dynamicstyles)

_Xamarin.Forms inclut six styles dynamiques, connus comme des styles de périphérique, dans la classe Device.Styles._

Le *appareil* styles sont :

- [`BodyStyle`](xref:Xamarin.Forms.Device.Styles.BodyStyle)
- [`CaptionStyle`](xref:Xamarin.Forms.Device.Styles.CaptionStyle)
- [`ListItemDetailTextStyle`](xref:Xamarin.Forms.Device.Styles.ListItemDetailTextStyle)
- [`ListItemTextStyle`](xref:Xamarin.Forms.Device.Styles.ListItemTextStyle)
- [`SubtitleStyle`](xref:Xamarin.Forms.Device.Styles.SubtitleStyle)
- [`TitleStyle`](xref:Xamarin.Forms.Device.Styles.TitleStyle)

Tous les six styles applicable uniquement aux [ `Label` ](xref:Xamarin.Forms.Label) instances. Par exemple, un `Label` qui affiche le corps d’un paragraphe peut définir son [ `Style` ](xref:Xamarin.Forms.NavigableElement.Style) propriété [ `BodyStyle` ](xref:Xamarin.Forms.Device.Styles.BodyStyle).

L’exemple de code suivant montre comment utiliser le *appareil* styles dans une page XAML :

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

Les styles de l’appareil sont liés à l’utilisation de la `DynamicResource` extension de balisage. La nature dynamique des styles peut être consultée dans iOS en modifiant le **accessibilité** paramètres pour la taille du texte. L’apparence de la *appareil* styles est différente sur chaque plateforme, comme indiqué dans les captures d’écran suivante :

![](device-images/device-styles.png "Styles d’appareils sur chaque plateforme")

*APPAREIL* styles peuvent également être dérivés en définissant le [ `BaseResourceKey` ](xref:Xamarin.Forms.Style.BaseResourceKey) propriété le nom de clé pour le style de l’appareil. Dans l’exemple de code ci-dessus, `myBodyStyle` hérite [ `BodyStyle` ](xref:Xamarin.Forms.Device.Styles.BodyStyle) et définit une couleur de texte accentuées. Pour plus d’informations sur l’héritage de style dynamique, consultez [l’héritage de Style dynamique](~/xamarin-forms/user-interface/styles/xaml/dynamic.md#dynamic-style-inheritance).

L’exemple de code suivant montre la page équivalente dans C# :

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

Le [ `Style` ](xref:Xamarin.Forms.NavigableElement.Style) propriété de chaque [ `Label` ](xref:Xamarin.Forms.Label) instance est définie sur la propriété appropriée à partir de la [ `Devices.Styles` ](xref:Xamarin.Forms.Device.Styles) classe.

## <a name="accessibility"></a>Accessibilité

Le *appareil* styles respectent les préférences d’accessibilité, afin de tailles de police change les préférences d’accessibilité sont modifiés sur chaque plateforme. Par conséquent, pour prendre en charge de texte accessible, vérifiez que le *appareil* styles sont utilisés comme base pour les styles de texte au sein de votre application.

Les captures d’écran suivantes montrent les styles de périphérique sur chaque plateforme, avec la plus petite taille de police accessible :

[![](device-images/minimum-size.png "Styles de périphérique de petite taille accessible sur chaque plateforme")](device-images/minimum-size-large.png#lightbox "Styles petit appareil Accessible sur chaque plateforme")

Les captures d’écran suivantes montrent les styles de périphérique sur chaque plateforme, avec la plus grande taille de police accessible :

![](device-images/maximum-size.png "Styles d’unité volumineuse accessible sur chaque plateforme")

## <a name="related-links"></a>Liens connexes

- [Styles de texte](~/xamarin-forms/user-interface/text/styles.md)
- [Extensions de balisage XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Styles dynamiques (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-dynamicstyles)
- [Utilisation des Styles (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [Device.Styles](xref:Xamarin.Forms.Device.Styles)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
- [Style](xref:Xamarin.Forms.Style)
- [Setter](xref:Xamarin.Forms.Setter)
