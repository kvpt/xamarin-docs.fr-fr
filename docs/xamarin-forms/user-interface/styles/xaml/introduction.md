---
title: Présentation des styles Xamarin. Forms
description: Les styles permettent de personnaliser l’apparence des éléments visuels. Les styles sont définis pour un type spécifique et contiennent des valeurs pour les propriétés disponibles sur ce type.
ms.prod: xamarin
ms.assetid: 3FF899C0-6CFB-4C1D-837D-9E9E10181967
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/27/2016
ms.openlocfilehash: 35f8dad3590c07ceb3c93aa735b8c02d75098498
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2019
ms.locfileid: "70228169"
---
# <a name="introduction-to-xamarinforms-styles"></a>Présentation des styles Xamarin. Forms

_Les styles permettent de personnaliser l’apparence des éléments visuels. Les styles sont définis pour un type spécifique et contiennent des valeurs pour les propriétés disponibles sur ce type._

Les applications Xamarin. Forms contiennent souvent plusieurs contrôles qui ont une apparence identique. Par exemple, une application peut avoir plusieurs instances de [`Label`](xref:Xamarin.Forms.Label) qui ont les mêmes options de police et options de disposition, comme indiqué dans l’exemple de code XAML suivant :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
    x:Class="Styles.NoStylesPage"
    Title="No Styles"
    IconImageSource="xaml.png">
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <Label Text="These labels"
                   HorizontalOptions="Center"
                   VerticalOptions="CenterAndExpand"
                   FontSize="Large" />
            <Label Text="are not"
                   HorizontalOptions="Center"
                   VerticalOptions="CenterAndExpand"
                   FontSize="Large" />
            <Label Text="using styles"
                   HorizontalOptions="Center"
                   VerticalOptions="CenterAndExpand"
                   FontSize="Large" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

L’exemple de code suivant montre la page équivalente créée en C# :

```csharp
public class NoStylesPageCS : ContentPage
{
    public NoStylesPageCS ()
    {
        Title = "No Styles";
        IconImageSource = "csharp.png";
        Padding = new Thickness (0, 20, 0, 0);

        Content = new StackLayout {
            Children = {
                new Label {
                    Text = "These labels",
                    HorizontalOptions = LayoutOptions.Center,
                    VerticalOptions = LayoutOptions.CenterAndExpand,
                    FontSize = Device.GetNamedSize (NamedSize.Large, typeof(Label))
                },
                new Label {
                    Text = "are not",
                    HorizontalOptions = LayoutOptions.Center,
                    VerticalOptions = LayoutOptions.CenterAndExpand,
                    FontSize = Device.GetNamedSize (NamedSize.Large, typeof(Label))
                },
                new Label {
                    Text = "using styles",
                    HorizontalOptions = LayoutOptions.Center,
                    VerticalOptions = LayoutOptions.CenterAndExpand,
                    FontSize = Device.GetNamedSize (NamedSize.Large, typeof(Label))
                }
            }
        };
    }
}
```

Chaque [`Label`](xref:Xamarin.Forms.Label) instance a des valeurs de propriété identiques pour contrôler l’apparence du texte affiché par le `Label`. Cela donne l’affichage illustré dans les captures d’écran suivantes :

[Apparence ![Label sans styles](introduction-images/no-styles.png)](introduction-images/no-styles-large.png#lightbox)

La définition de l’apparence de chaque contrôle individuel peut être répétitive et sujette aux erreurs. Au lieu de cela, vous pouvez créer un style qui définit l’apparence, puis l’appliquer aux contrôles requis.

## <a name="create-a-style"></a>Créer un style

La classe [`Style`](xref:Xamarin.Forms.Style) regroupe une collection de valeurs de propriété dans un objet qui peut ensuite être appliqué à plusieurs instances d’éléments visuels. Cela permet de réduire le balisage répétitif et de faciliter la modification de l’apparence des applications.

Bien que les styles aient été conçus principalement pour les applications basées sur XAML, ils peuvent C#également être créés dans :

- les instances [`Style`](xref:Xamarin.Forms.Style) créées en XAML sont généralement définies dans une [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) qui est assignée à la collection [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) d’un contrôle, d’une page ou à la collection [`Resources`](xref:Xamarin.Forms.Application.Resources) de l’application.
- les instances [`Style`](xref:Xamarin.Forms.Style) créées C# dans sont généralement définies dans la classe de la page, ou dans une classe qui peut être accédée globalement.

Le fait de choisir où définir [`Style`](xref:Xamarin.Forms.Style) impacte l’emplacement où il peut être utilisé :

- les instances de [`Style`](xref:Xamarin.Forms.Style) définies au niveau du contrôle ne peuvent être appliquées qu’au contrôle et à ses enfants.
- les instances de [`Style`](xref:Xamarin.Forms.Style) définies au niveau de la page ne peuvent être appliquées qu’à la page et à ses enfants.
- les instances de [`Style`](xref:Xamarin.Forms.Style) définies au niveau de l’application peuvent être appliquées dans l’ensemble de l’application.

Chaque [`Style`](xref:Xamarin.Forms.Style) instance contient une collection d’un ou plusieurs objets [`Setter`](xref:Xamarin.Forms.Setter) , chaque `Setter` ayant un [`Property`](xref:Xamarin.Forms.Setter.Property) et un [`Value`](xref:Xamarin.Forms.Setter.Value). Le `Property` est le nom de la propriété pouvant être liée de l’élément auquel le style est appliqué, et le `Value` est la valeur qui est appliquée à la propriété.

Chaque [`Style`](xref:Xamarin.Forms.Style) instance peut être *explicite*ou *implicite*:

- Une instance de [`Style`](xref:Xamarin.Forms.Style) *explicite* est définie en spécifiant une [`TargetType`](xref:Xamarin.Forms.Style.TargetType) et une valeur `x:Key`, et en affectant à la propriété [`Style`](xref:Xamarin.Forms.NavigableElement.Style) de l’élément cible la référence `x:Key`. Pour plus d’informations sur les styles *explicites* , consultez [styles explicites](~/xamarin-forms/user-interface/styles/explicit.md).
- Une instance de [`Style`](xref:Xamarin.Forms.Style) *implicite* est définie en spécifiant uniquement une [`TargetType`](xref:Xamarin.Forms.Style.TargetType). L’instance de `Style` est ensuite automatiquement appliquée à tous les éléments de ce type. Notez que les sous-classes du `TargetType` n’ont pas automatiquement le `Style` appliqué. Pour plus d’informations sur les styles *implicites* , consultez [styles implicites](~/xamarin-forms/user-interface/styles/implicit.md).

Lorsque vous créez un [`Style`](xref:Xamarin.Forms.Style), la propriété [`TargetType`](xref:Xamarin.Forms.Style.TargetType) est toujours requise. L’exemple de code suivant montre un style *explicite* (notez le `x:Key`) créé en XAML :

```xaml
<Style x:Key="labelStyle" TargetType="Label">
    <Setter Property="HorizontalOptions" Value="Center" />
    <Setter Property="VerticalOptions" Value="CenterAndExpand" />
    <Setter Property="FontSize" Value="Large" />
</Style>
```

Pour appliquer un `Style`, l’objet cible doit être un [`VisualElement`](xref:Xamarin.Forms.VisualElement) qui correspond à la valeur de propriété [`TargetType`](xref:Xamarin.Forms.Style.TargetType) de l' `Style`, comme indiqué dans l’exemple de code XAML suivant :

```xaml
<Label Text="Demonstrating an explicit style" Style="{StaticResource labelStyle}" />
```

Les styles inférieurs dans la hiérarchie d’affichage ont priorité sur ceux définis plus haut. Par exemple, la définition d’un [`Style`](xref:Xamarin.Forms.Style) qui définit [`Label.TextColor`](xref:Xamarin.Forms.Label.TextColor) à `Red` au niveau de l’application est remplacée par un style de niveau page qui définit `Label.TextColor` sur `Green`. De même, un style de niveau page est remplacé par un style de niveau contrôle. En outre, si `Label.TextColor` est définie directement sur une propriété de contrôle, cela est prioritaire sur tous les styles.

Les Articles de cette section décrivent et expliquent comment créer et appliquer des styles *explicites* et *implicites* , comment créer des styles globaux, l’héritage de style, comment répondre aux modifications de style au moment de l’exécution et comment utiliser les styles intégrés inclus dans Xamarin. Forms.

> [!NOTE]
> **Qu’est-ce que StyleId ?**
>
> Avant Xamarin. Forms 2,2, la propriété [`StyleId`](xref:Xamarin.Forms.Element.StyleId) était utilisée pour identifier des éléments individuels dans une application pour l’identification dans les tests de l’interface utilisateur et dans les moteurs de thèmes tels que Pixate. Toutefois, Xamarin. Forms 2,2 a introduit la propriété [`AutomationId`](xref:Xamarin.Forms.Element.AutomationId) , qui a remplacé la propriété [`StyleId`](xref:Xamarin.Forms.Element.StyleId) .

## <a name="related-links"></a>Liens connexes

- [Extensions de balisage XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Style](xref:Xamarin.Forms.Style)
- [Déclaré](xref:Xamarin.Forms.Setter)
