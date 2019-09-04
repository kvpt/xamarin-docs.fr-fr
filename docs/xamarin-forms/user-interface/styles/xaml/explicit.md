---
title: Styles explicites dans Xamarin.Forms
description: Un style explicite est celle qui est appliquée sélectivement à des contrôles en définissant leurs propriétés de Style. Cet article explique comment utiliser les styles explicites dans une application Xamarin.Forms.
ms.prod: xamarin
ms.assetid: C0DF9F8F-B431-4374-A574-325BC3C41A3B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
ms.openlocfilehash: b7888ea29dd02733059346bca21fec0a4c510c2e
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/03/2019
ms.locfileid: "70227997"
---
# <a name="explicit-styles-in-xamarinforms"></a>Styles explicites dans Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)

_Un style explicite est celle qui est appliquée sélectivement à des contrôles en définissant leurs propriétés de Style._

## <a name="create-an-explicit-style-in-xaml"></a>Créer un style explicite en XAML

Pour déclarer un [ `Style` ](xref:Xamarin.Forms.Style) au niveau de la page, un [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) doit être ajouté à la page et ensuite un ou plusieurs `Style` déclarations peuvent être incluses dans le `ResourceDictionary`. Un `Style` est effectuée *explicite* en donnant sa déclaration un `x:Key` attribut, ce qui lui donne une clé descriptive dans la `ResourceDictionary`. *Explicite* styles doivent ensuite être appliquées à des éléments spécifiques de visual en définissant leurs [ `Style` ](xref:Xamarin.Forms.NavigableElement.Style) propriétés.

Le code suivant montre l’exemple *explicite* styles déclaré dans XAML dans une page `ResourceDictionary` et appliquée à la page [ `Label` ](xref:Xamarin.Forms.Label) instances :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.ExplicitStylesPage" Title="Explicit" IconImageSource="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="labelRedStyle" TargetType="Label">
                <Setter Property="HorizontalOptions"
                        Value="Center" />
                <Setter Property="VerticalOptions"
                        Value="CenterAndExpand" />
                <Setter Property="FontSize" Value="Large" />
                <Setter Property="TextColor" Value="Red" />
            </Style>
            <Style x:Key="labelGreenStyle" TargetType="Label">
                ...
                <Setter Property="TextColor" Value="Green" />
            </Style>
            <Style x:Key="labelBlueStyle" TargetType="Label">
                ...
                <Setter Property="TextColor" Value="Blue" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <Label Text="These labels"
                   Style="{StaticResource labelRedStyle}" />
            <Label Text="are demonstrating"
                   Style="{StaticResource labelGreenStyle}" />
            <Label Text="explicit styles,"
                   Style="{StaticResource labelBlueStyle}" />
            <Label Text="and an explicit style override"
                   Style="{StaticResource labelBlueStyle}"
                   TextColor="Teal" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

Le [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) définit trois *explicite* styles sont appliqués à la page [ `Label` ](xref:Xamarin.Forms.Label) instances. Chaque `Style` est utilisé pour afficher du texte dans une couleur différente, et d’établir la police options de disposition de taille et horizontal et vertical. Chaque `Style` est appliqué à un autre `Label` en définissant son [ `Style` ](xref:Xamarin.Forms.NavigableElement.Style) propriétés à l’aide de la `StaticResource` extension de balisage. Cela donne l’affichage illustré dans les captures d’écran suivantes :

[![Exemple de styles explicites](explicit-images/explicit-styles.png)](explicit-images/explicit-styles-large.png#lightbox)

En outre, la dernière [ `Label` ](xref:Xamarin.Forms.Label) a un [ `Style` ](xref:Xamarin.Forms.Style) appliquée, mais remplace également la [ `TextColor` ](xref:Xamarin.Forms.Label.TextColor) propriété à un autre `Color`valeur.

### <a name="create-an-explicit-style-at-the-control-level"></a>Créer un style explicite au niveau du contrôle

Outre la création de *explicite* styles au niveau de la page, ils peuvent également être créés au niveau du contrôle, comme indiqué dans l’exemple de code suivant :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.ExplicitStylesPage" Title="Explicit" IconImageSource="xaml.png">
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <StackLayout.Resources>
                <ResourceDictionary>
                    <Style x:Key="labelRedStyle" TargetType="Label">
                      ...
                    </Style>
                    ...
                </ResourceDictionary>
            </StackLayout.Resources>
            <Label Text="These labels" Style="{StaticResource labelRedStyle}" />
            ...
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

Dans cet exemple, le *explicite* [ `Style` ](xref:Xamarin.Forms.Style) instances sont affectés à la [ `Resources` ](xref:Xamarin.Forms.VisualElement.Resources) collection de la [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) contrôle. Les styles peuvent ensuite être appliqués au contrôle et ses enfants.

Pour plus d’informations sur la création de styles dans une application [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary), consultez [Styles globaux](~/xamarin-forms/user-interface/styles/application.md).

## <a name="create-an-explicit-style-in-c35"></a>Créer un style explicite en C&#35;

[`Style`](xref:Xamarin.Forms.Style) instances peuvent être ajoutées à une page [ `Resources` ](xref:Xamarin.Forms.VisualElement.Resources) collection en C# en créant un nouveau [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)et puis en ajoutant le `Style` instances pour le `ResourceDictionary`, comme illustré dans la exemple de code suivant :

```csharp
public class ExplicitStylesPageCS : ContentPage
{
    public ExplicitStylesPageCS ()
    {
        var labelRedStyle = new Style (typeof(Label)) {
            Setters = {
                ...
                new Setter { Property = Label.TextColorProperty, Value = Color.Red    }
            }
        };
        var labelGreenStyle = new Style (typeof(Label)) {
            Setters = {
                ...
                new Setter { Property = Label.TextColorProperty, Value = Color.Green }
            }
        };
        var labelBlueStyle = new Style (typeof(Label)) {
            Setters = {
                ...
                new Setter { Property = Label.TextColorProperty, Value = Color.Blue }
            }
        };

        Resources = new ResourceDictionary ();
        Resources.Add ("labelRedStyle", labelRedStyle);
        Resources.Add ("labelGreenStyle", labelGreenStyle);
        Resources.Add ("labelBlueStyle", labelBlueStyle);
        ...

        Content = new StackLayout {
            Children = {
                new Label { Text = "These labels",
                            Style = (Style)Resources ["labelRedStyle"] },
                new Label { Text = "are demonstrating",
                            Style = (Style)Resources ["labelGreenStyle"] },
                new Label { Text = "explicit styles,",
                            Style = (Style)Resources ["labelBlueStyle"] },
                new Label {    Text = "and an explicit style override",
                            Style = (Style)Resources ["labelBlueStyle"], TextColor = Color.Teal }
            }
        };
    }
}
```

Le constructeur définit trois *explicite* styles sont appliqués à la page [ `Label` ](xref:Xamarin.Forms.Label) instances. Chaque *explicite* [ `Style` ](xref:Xamarin.Forms.Style) est ajouté à la [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) à l’aide de la [ `Add` ](xref:Xamarin.Forms.ResourceDictionary.Add(System.String,System.Object)) (méthode), en spécifiant un `key` chaîne pour faire référence à la `Style` instance. Chaque `Style` est appliqué à un autre `Label` en définissant leurs [ `Style` ](xref:Xamarin.Forms.NavigableElement.Style) propriétés.

Toutefois, il n’existe aucun avantage un [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) ici. Au lieu de cela, [ `Style` ](xref:Xamarin.Forms.Style) instances peuvent être affectées directement à la [ `Style` ](xref:Xamarin.Forms.NavigableElement.Style) propriétés des éléments visuels requis et le `ResourceDictionary` peut être supprimé, comme illustré dans l’exemple suivant exemple de code :

```csharp
public class ExplicitStylesPageCS : ContentPage
{
    public ExplicitStylesPageCS ()
    {
        var labelRedStyle = new Style (typeof(Label)) {
            ...
        };
        var labelGreenStyle = new Style (typeof(Label)) {
            ...
        };
        var labelBlueStyle = new Style (typeof(Label)) {
            ...
        };
        ...
        Content = new StackLayout {
            Children = {
                new Label { Text = "These labels", Style = labelRedStyle },
                new Label { Text = "are demonstrating", Style = labelGreenStyle },
                new Label { Text = "explicit styles,", Style = labelBlueStyle },
                new Label { Text = "and an explicit style override", Style = labelBlueStyle,
                            TextColor = Color.Teal }
            }
        };
    }
}
```

Le constructeur définit trois *explicite* styles sont appliqués à la page [ `Label` ](xref:Xamarin.Forms.Label) instances. Chaque `Style` est utilisé pour afficher du texte dans une couleur différente, et d’établir la police options de disposition de taille et horizontal et vertical. Chaque `Style` est appliqué à un autre `Label` en définissant son [ `Style` ](xref:Xamarin.Forms.NavigableElement.Style) propriétés. En outre, la dernière `Label` a un `Style` appliquée, mais remplace également la `TextColor` propriété vers un autre `Color` valeur.

## <a name="related-links"></a>Liens connexes

- [Extensions de balisage XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Styles de base (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)
- [Utilisation des Styles (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
- [Style](xref:Xamarin.Forms.Style)
- [Setter](xref:Xamarin.Forms.Setter)
