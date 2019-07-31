---
title: Héritage de style dans Xamarin.Forms
description: Styles peuvent hériter d’autres styles pour réduire la duplication et permettre la réutilisation. Cet article explique comment effectuer l’héritage de style dans une application Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 67A3A39C-8CC0-446D-8162-FFA73582D3B8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
ms.openlocfilehash: 24f6eac93dd3095df70bbb326388cd9ed47f395c
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68657055"
---
# <a name="style-inheritance-in-xamarinforms"></a>Héritage de style dans Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)

_Styles peuvent hériter d’autres styles pour réduire la duplication et permettre la réutilisation._

## <a name="style-inheritance-in-xaml"></a>Héritage de style en XAML

L’héritage de style est effectuée en affectant la [ `Style.BasedOn` ](xref:Xamarin.Forms.Style.BasedOn) propriété à un existant [ `Style` ](xref:Xamarin.Forms.Style). Dans XAML, cela est accompli en définissant le `BasedOn` propriété à un `StaticResource` extension de balisage qui fait référence à un créé précédemment `Style`. Dans C#, cela est accompli en définissant le `BasedOn` propriété un `Style` instance.

Les styles qui héritent d’un style de base peuvent inclure [ `Setter` ](xref:Xamarin.Forms.Setter) instances pour les nouvelles propriétés, ou les utiliser pour remplacer les styles du style de base. En outre, les styles qui héritent d’un style de base doivent cibler le même type, ou un type qui dérive du type ciblé par le style de base. Par exemple, si les cibles d’un style de base [ `View` ](xref:Xamarin.Forms.View) instances, les styles sont basées sur le style de base peuvent cibler `View` instances ou les types dérivés de la `View` classe, telle que [ `Label` ](xref:Xamarin.Forms.Label) et [ `Button` ](xref:Xamarin.Forms.Button) instances.

Le code suivant illustre *explicite* l’héritage de style dans une page XAML :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.StyleInheritancePage" Title="Inheritance" IconImageSource="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="baseStyle" TargetType="View">
                <Setter Property="HorizontalOptions"
                        Value="Center" />
                <Setter Property="VerticalOptions"
                        Value="CenterAndExpand" />
            </Style>
            <Style x:Key="labelStyle" TargetType="Label"
                   BasedOn="{StaticResource baseStyle}">
                ...
                <Setter Property="TextColor" Value="Teal" />
            </Style>
            <Style x:Key="buttonStyle" TargetType="Button"
                   BasedOn="{StaticResource baseStyle}">
                <Setter Property="BorderColor" Value="Lime" />
                ...
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <Label Text="These labels"
                   Style="{StaticResource labelStyle}" />
            ...
            <Button Text="So is the button"
                    Style="{StaticResource buttonStyle}" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

Le `baseStyle` cibles [ `View` ](xref:Xamarin.Forms.View) instances et définit le [ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions) et [ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions) propriétés. Le `baseStyle` n’est pas définie directement sur tous les contrôles. Au lieu de cela, `labelStyle` et `buttonStyle` héritent de celui-ci, définition des valeurs de propriété pouvant être liée supplémentaire. Le `labelStyle` et `buttonStyle` sont ensuite appliquées à la [ `Label` ](xref:Xamarin.Forms.Label) instances et [ `Button` ](xref:Xamarin.Forms.Button) instance, en définissant leurs [ `Style` ](xref:Xamarin.Forms.NavigableElement.Style) propriétés. Cela provoque l’affichage indiqué dans les captures d’écran suivante :

[![](inheritance-images/style-inheritance.png)](inheritance-images/style-inheritance-large.png#lightbox)

> [!NOTE]
> Un style implicite peut être dérivé d’un style explicite, mais un style explicite ne peut pas être dérivé d’un style implicite.

### <a name="respecting-the-inheritance-chain"></a>Respect de la chaîne d’héritage

Un style peut uniquement hériter de styles au même niveau, ou plus, dans la hiérarchie d’affichage. Cela signifie que :

- Une ressource de niveau application peut uniquement hériter d’autres ressources de niveau application.
- Une ressource de niveau page peut hériter de ressources de niveau application et d’autres ressources de niveau page.
- Une ressource au niveau de contrôle peut hériter les ressources de niveau application, les ressources de niveau page et d’autres ressources de niveau de contrôle.

Cette chaîne d’héritage est illustrée dans l’exemple de code suivant :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.StyleInheritancePage" Title="Inheritance" IconImageSource="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="baseStyle" TargetType="View">
              ...
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <StackLayout.Resources>
                <ResourceDictionary>
                    <Style x:Key="labelStyle" TargetType="Label" BasedOn="{StaticResource baseStyle}">
                      ...
                    </Style>
                    <Style x:Key="buttonStyle" TargetType="Button" BasedOn="{StaticResource baseStyle}">
                      ...
                    </Style>
                </ResourceDictionary>
            </StackLayout.Resources>
            ...
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

Dans cet exemple, `labelStyle` et `buttonStyle` est des ressources de niveau de contrôle alors que `baseStyle` est une ressource de niveau page. Toutefois, même si `labelStyle` et `buttonStyle` héritent `baseStyle`, il n’est pas possible pour `baseStyle` d’hériter de `labelStyle` ou `buttonStyle`en raison de leurs emplacements respectifs dans la hiérarchie d’affichage.

## <a name="style-inheritance-in-c35"></a>Héritage de style en C&#35;

La page C# équivalente, où [ `Style` ](xref:Xamarin.Forms.Style) instances affectées directement au [ `Style` ](xref:Xamarin.Forms.NavigableElement.Style) propriétés des contrôles requis, est illustré dans l’exemple de code suivant :

```csharp
public class StyleInheritancePageCS : ContentPage
{
    public StyleInheritancePageCS ()
    {
        var baseStyle = new Style (typeof(View)) {
            Setters = {
                new Setter {
                    Property = View.HorizontalOptionsProperty, Value = LayoutOptions.Center    },
                ...
            }
        };

        var labelStyle = new Style (typeof(Label)) {
            BasedOn = baseStyle,
            Setters = {
                ...
                new Setter { Property = Label.TextColorProperty, Value = Color.Teal    }
            }
        };

        var buttonStyle = new Style (typeof(Button)) {
            BasedOn = baseStyle,
            Setters = {
                new Setter { Property = Button.BorderColorProperty, Value =    Color.Lime },
                ...
            }
        };
        ...

        Content = new StackLayout {
            Children = {
                new Label { Text = "These labels", Style = labelStyle },
                ...
                new Button { Text = "So is the button", Style = buttonStyle }
            }
        };
    }
}
```

Le `baseStyle` cibles [ `View` ](xref:Xamarin.Forms.View) instances et définit le [ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions) et [ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions) propriétés. Le `baseStyle` n’est pas définie directement sur tous les contrôles. Au lieu de cela, `labelStyle` et `buttonStyle` héritent de celui-ci, définition des valeurs de propriété pouvant être liée supplémentaire. Le `labelStyle` et `buttonStyle` sont ensuite appliquées à la [ `Label` ](xref:Xamarin.Forms.Label) instances et [ `Button` ](xref:Xamarin.Forms.Button) instance, en définissant leurs [ `Style` ](xref:Xamarin.Forms.NavigableElement.Style) propriétés.

## <a name="related-links"></a>Liens connexes

- [Extensions de balisage XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Styles de base (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)
- [Utilisation des Styles (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
- [Style](xref:Xamarin.Forms.Style)
- [Setter](xref:Xamarin.Forms.Setter)
