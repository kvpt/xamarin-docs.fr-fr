---
title: Présentation des Xamarin.Forms styles
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 5766af7da3a0cf550a2ccb3a926dad25fd7962eb
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84138817"
---
# <a name="introduction-to-xamarinforms-styles"></a>Présentation des Xamarin.Forms styles

_Les styles permettent de personnaliser l’apparence des éléments visuels. Les styles sont définis pour un type spécifique et contiennent des valeurs pour les propriétés disponibles sur ce type._

Xamarin.Formsles applications contiennent souvent plusieurs contrôles qui ont une apparence identique. Par exemple, une application peut avoir plusieurs [`Label`](xref:Xamarin.Forms.Label) instances qui ont les mêmes options de police et options de disposition, comme indiqué dans l’exemple de code XAML suivant :

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

Chaque [`Label`](xref:Xamarin.Forms.Label) instance a des valeurs de propriété identiques pour contrôler l’apparence du texte affiché par le `Label` . Cela donne l’affichage illustré dans les captures d’écran suivantes :

[![Apparence des étiquettes sans styles](introduction-images/no-styles.png)](introduction-images/no-styles-large.png#lightbox)

La définition de l’apparence de chaque contrôle individuel peut être répétitive et sujette aux erreurs. Au lieu de cela, vous pouvez créer un style qui définit l’apparence, puis l’appliquer aux contrôles requis.

## <a name="create-a-style"></a>Créer un style

La [`Style`](xref:Xamarin.Forms.Style) classe regroupe une collection de valeurs de propriété dans un objet qui peut ensuite être appliqué à plusieurs instances d’éléments visuels. Cela permet de réduire le balisage répétitif et de faciliter la modification de l’apparence des applications.

Bien que les styles aient été conçus principalement pour les applications basées sur XAML, ils peuvent également être créés en C# :

- [`Style`](xref:Xamarin.Forms.Style)les instances créées en XAML sont généralement définies dans une [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) qui est assignée à la [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) collection d’un contrôle, d’une page ou à la [`Resources`](xref:Xamarin.Forms.Application.Resources) collection de l’application.
- [`Style`](xref:Xamarin.Forms.Style)les instances créées en C# sont généralement définies dans la classe de la page, ou dans une classe qui peut être accédée globalement.

Choix de l’emplacement de définition d’un [`Style`](xref:Xamarin.Forms.Style) impact à utiliser :

- [`Style`](xref:Xamarin.Forms.Style)les instances définies au niveau du contrôle ne peuvent être appliquées qu’au contrôle et à ses enfants.
- [`Style`](xref:Xamarin.Forms.Style)les instances définies au niveau de la page ne peuvent être appliquées qu’à la page et à ses enfants.
- [`Style`](xref:Xamarin.Forms.Style)les instances définies au niveau de l’application peuvent être appliquées dans l’ensemble de l’application.

Chaque [`Style`](xref:Xamarin.Forms.Style) instance contient une collection d’un ou de plusieurs [`Setter`](xref:Xamarin.Forms.Setter) objets, chacun `Setter` ayant un [`Property`](xref:Xamarin.Forms.Setter.Property) et un [`Value`](xref:Xamarin.Forms.Setter.Value) . `Property` est le nom de la propriété pouvant être liée de l’élément auquel le style est appliqué, et `Value` est la valeur qui est appliquée à la propriété.

Chaque [`Style`](xref:Xamarin.Forms.Style) instance peut être *explicite*ou *implicite*:

- Une *explicit* [`Style`](xref:Xamarin.Forms.Style) instance explicite est définie en spécifiant un [`TargetType`](xref:Xamarin.Forms.Style.TargetType) et une `x:Key` valeur, et en affectant à la propriété de l’élément cible [`Style`](xref:Xamarin.Forms.NavigableElement.Style) la `x:Key` référence. Pour plus d’informations sur les styles *explicites* , consultez [styles explicites](~/xamarin-forms/user-interface/styles/explicit.md).
- Une instance *implicite* [`Style`](xref:Xamarin.Forms.Style) est définie en spécifiant uniquement un [`TargetType`](xref:Xamarin.Forms.Style.TargetType) . L' `Style` instance sera alors automatiquement appliquée à tous les éléments de ce type. Notez que les sous-classes du `TargetType` n’ont pas automatiquement le `Style` appliqué. Pour plus d’informations sur les styles *implicites* , consultez [styles implicites](~/xamarin-forms/user-interface/styles/implicit.md).

Lors de la création d’un [`Style`](xref:Xamarin.Forms.Style) , la [`TargetType`](xref:Xamarin.Forms.Style.TargetType) propriété est toujours obligatoire. L’exemple de code suivant montre un style *explicite* (Notez le `x:Key` ) créé en XAML :

```xaml
<Style x:Key="labelStyle" TargetType="Label">
    <Setter Property="HorizontalOptions" Value="Center" />
    <Setter Property="VerticalOptions" Value="CenterAndExpand" />
    <Setter Property="FontSize" Value="Large" />
</Style>
```

Pour appliquer un `Style` , l’objet cible doit être un [`VisualElement`](xref:Xamarin.Forms.VisualElement) qui correspond à la [`TargetType`](xref:Xamarin.Forms.Style.TargetType) valeur de propriété du `Style` , comme indiqué dans l’exemple de code XAML suivant :

```xaml
<Label Text="Demonstrating an explicit style" Style="{StaticResource labelStyle}" />
```

Les styles inférieurs dans la hiérarchie d’affichage ont priorité sur ceux définis plus haut. Par exemple, la définition [`Style`](xref:Xamarin.Forms.Style) d’un qui affecte [`Label.TextColor`](xref:Xamarin.Forms.Label.TextColor) à au `Red` niveau de l’application est remplacée par un style de niveau page qui affecte à la valeur `Label.TextColor` `Green` . De même, un style de niveau page est remplacé par un style de niveau contrôle. En outre, si `Label.TextColor` est défini directement sur une propriété de contrôle, il est prioritaire sur tous les styles.

Les Articles de cette section décrivent et expliquent comment créer et appliquer des styles *explicites* et *implicites* , comment créer des styles globaux, l’héritage de style, comment répondre aux modifications de style au moment de l’exécution et comment utiliser les styles intégrés inclus dans Xamarin.Forms .

> [!NOTE]
> **Qu’est-ce que StyleId ?**
>
> Avant Xamarin.Forms le 2,2, la [`StyleId`](xref:Xamarin.Forms.Element.StyleId) propriété était utilisée pour identifier des éléments individuels dans une application pour l’identification dans les tests de l’interface utilisateur et dans les moteurs de thèmes tels que Pixate. Toutefois, Xamarin.Forms 2,2 a introduit la [`AutomationId`](xref:Xamarin.Forms.Element.AutomationId) propriété, qui a remplacé la [`StyleId`](xref:Xamarin.Forms.Element.StyleId) propriété.

## <a name="related-links"></a>Liens connexes

- [Extensions de balisage XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Style](xref:Xamarin.Forms.Style)
- [Setter](xref:Xamarin.Forms.Setter)
