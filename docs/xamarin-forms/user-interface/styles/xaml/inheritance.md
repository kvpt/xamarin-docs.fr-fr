---
title: Héritage de style dansXamarin.Forms
description: Les styles peuvent hériter d’autres styles pour réduire la duplication et permettre la réutilisation. Cet article explique comment effectuer un héritage de style dans une Xamarin.Forms application.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 80cc419ae098f4a0cbbd782785c0ec5ba03fa703
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84138955"
---
# <a name="style-inheritance-in-xamarinforms"></a>Héritage de style dansXamarin.Forms

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)

_Les styles peuvent hériter d’autres styles pour réduire la duplication et permettre la réutilisation._

## <a name="style-inheritance-in-xaml"></a>Héritage de style en XAML

L’héritage de style est effectué en affectant [`Style.BasedOn`](xref:Xamarin.Forms.Style.BasedOn) à la propriété une valeur existante [`Style`](xref:Xamarin.Forms.Style) . En XAML, cela est possible en affectant `BasedOn` à la propriété une `StaticResource` extension de balisage qui référence un créé précédemment `Style` . En C#, cela est possible en affectant `BasedOn` à la propriété une `Style` instance.

Les styles qui héritent d’un style de base peuvent inclure [`Setter`](xref:Xamarin.Forms.Setter) des instances de nouvelles propriétés ou les utiliser pour remplacer les styles du style de base. En outre, les styles qui héritent d’un style de base doivent cibler le même type ou un type qui dérive du type ciblé par le style de base. Par exemple, si un style de base cible des [`View`](xref:Xamarin.Forms.View) instances, les styles basés sur le style de base peuvent cibler `View` des instances ou des types qui dérivent de la `View` classe, tels que les [`Label`](xref:Xamarin.Forms.Label) [`Button`](xref:Xamarin.Forms.Button) instances et.

Le code suivant illustre l’héritage de style *explicite* dans une page XAML :

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

Les `baseStyle` [`View`](xref:Xamarin.Forms.View) instances cibles et définit les [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) Propriétés et [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) . Le `baseStyle` n’est pas défini directement sur les contrôles. Au lieu `labelStyle` `buttonStyle` de cela, en définissant des valeurs de propriété pouvant être liées supplémentaires. Les `labelStyle` et `buttonStyle` sont ensuite appliqués aux [`Label`](xref:Xamarin.Forms.Label) instances et à l' [`Button`](xref:Xamarin.Forms.Button) instance, en définissant leurs [`Style`](xref:Xamarin.Forms.NavigableElement.Style) Propriétés. Cela donne l’affichage illustré dans les captures d’écran suivantes :

[![](inheritance-images/style-inheritance.png)](inheritance-images/style-inheritance-large.png#lightbox)

> [!NOTE]
> Un style implicite peut être dérivé d’un style explicite, mais un style explicite ne peut pas être dérivé d’un style implicite.

### <a name="respecting-the-inheritance-chain"></a>Respect de la chaîne d’héritage

Un style peut uniquement hériter de styles au même niveau ou au-dessus de la hiérarchie de la vue. Cela signifie que :

- Une ressource au niveau de l’application ne peut hériter que d’autres ressources de niveau application.
- Une ressource au niveau de la page peut hériter des ressources au niveau de l’application et d’autres ressources de niveau page.
- Une ressource au niveau du contrôle peut hériter des ressources au niveau de l’application, des ressources au niveau de la page et d’autres ressources de niveau contrôle.

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

Dans cet exemple, `labelStyle` et `buttonStyle` sont des ressources de niveau contrôle, tandis que `baseStyle` est une ressource au niveau de la page. Toutefois, bien que `labelStyle` et `buttonStyle` héritent de `baseStyle` , il n’est pas possible d' `baseStyle` hériter de `labelStyle` ou `buttonStyle` , en raison de leurs emplacements respectifs dans la hiérarchie de vue.

## <a name="style-inheritance-in-c35"></a>Héritage de style en C&#35;

La page C# équivalente, où les [`Style`](xref:Xamarin.Forms.Style) instances sont affectées directement aux [`Style`](xref:Xamarin.Forms.NavigableElement.Style) Propriétés des contrôles requis, est présentée dans l’exemple de code suivant :

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

Les `baseStyle` [`View`](xref:Xamarin.Forms.View) instances cibles et définit les [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) Propriétés et [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) . Le `baseStyle` n’est pas défini directement sur les contrôles. Au lieu `labelStyle` `buttonStyle` de cela, en définissant des valeurs de propriété pouvant être liées supplémentaires. Les `labelStyle` et `buttonStyle` sont ensuite appliqués aux [`Label`](xref:Xamarin.Forms.Label) instances et à l' [`Button`](xref:Xamarin.Forms.Button) instance, en définissant leurs [`Style`](xref:Xamarin.Forms.NavigableElement.Style) Propriétés.

## <a name="related-links"></a>Liens connexes

- [Extensions de balisage XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Styles de base (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)
- [Utilisation des styles (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
- [Style](xref:Xamarin.Forms.Style)
- [Setter](xref:Xamarin.Forms.Setter)
