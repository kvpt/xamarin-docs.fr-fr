---
title: Styles implicites dans Xamarin.Forms
description: Un style implicite est celui qui est utilisé par tous les contrôles de la même TargetType, sans nécessiter de chaque contrôle pour faire référence au style.
ms.prod: xamarin
ms.assetid: 02A75F3B-4389-49D4-A2F4-AFD473A4A161
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/30/2019
ms.openlocfilehash: d58ba81596cccf470b7246514d71f35968599880
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79305275"
---
# <a name="implicit-styles-in-xamarinforms"></a>Styles implicites dans Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)

_Un style implicite est un style qui est utilisé par tous les contrôles du même TargetType, sans qu’il soit nécessaire que chaque contrôle référence le style._

## <a name="create-an-implicit-style-in-xaml"></a>Créer un style implicite en XAML

Pour déclarer un [`Style`](xref:Xamarin.Forms.Style) au niveau de la page, un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) doit être ajouté à la page, puis une ou plusieurs déclarations `Style` peuvent être incluses dans le `ResourceDictionary`. Une `Style` est rendue *implicite* en ne spécifiant pas d’attribut `x:Key`. Le style sera ensuite appliqué aux éléments visuels qui correspondent exactement au `TargetType`, mais pas aux éléments dérivés de la valeur `TargetType`.

L’exemple de code suivant montre un style *implicite* déclaré en XAML dans le `ResourceDictionary`d’une page et appliqué aux instances de [`Entry`](xref:Xamarin.Forms.Entry) de la page :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" xmlns:local="clr-namespace:Styles;assembly=Styles" x:Class="Styles.ImplicitStylesPage" Title="Implicit" IconImageSource="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Entry">
                <Setter Property="HorizontalOptions" Value="Fill" />
                <Setter Property="VerticalOptions" Value="CenterAndExpand" />
                <Setter Property="BackgroundColor" Value="Yellow" />
                <Setter Property="FontAttributes" Value="Italic" />
                <Setter Property="TextColor" Value="Blue" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <Entry Text="These entries" />
            <Entry Text="are demonstrating" />
            <Entry Text="implicit styles," />
            <Entry Text="and an implicit style override" BackgroundColor="Lime" TextColor="Red" />
            <local:CustomEntry Text="Subclassed Entry is not receiving the style" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

Le [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) définit un style *implicite* unique qui est appliqué aux instances de [`Entry`](xref:Xamarin.Forms.Entry) de la page. Le `Style` est utilisé pour afficher du texte en bleu sur un arrière-plan jaune, tout en définissant d’autres options d’apparence. Le `Style` est ajouté au [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) de la page sans spécifier d’attribut `x:Key`. Par conséquent, le `Style` est appliqué à toutes les instances de `Entry` implicitement, car elles correspondent exactement à la propriété [`TargetType`](xref:Xamarin.Forms.Style.TargetType) du `Style`. Toutefois, le `Style` n’est pas appliqué à l’instance de `CustomEntry`, qui est une `Entry`sous-classée. Cela donne l’affichage illustré dans les captures d’écran suivantes :

[Exemple de styles implicites ![](implicit-images/implicit-styles.png)](implicit-images/implicit-styles-large.png#lightbox)

En outre, la quatrième [`Entry`](xref:Xamarin.Forms.Entry) remplace les propriétés [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) et [`TextColor`](xref:Xamarin.Forms.InputView.TextColor) du style implicite par différentes valeurs de `Color`.

### <a name="create-an-implicit-style-at-the-control-level"></a>Créer un style implicite au niveau du contrôle

Outre la création de styles *implicites* au niveau de la page, ils peuvent également être créés au niveau du contrôle, comme indiqué dans l’exemple de code suivant :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" xmlns:local="clr-namespace:Styles;assembly=Styles" x:Class="Styles.ImplicitStylesPage" Title="Implicit" IconImageSource="xaml.png">
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <StackLayout.Resources>
                <ResourceDictionary>
                    <Style TargetType="Entry">
                        <Setter Property="HorizontalOptions" Value="Fill" />
                        ...
                    </Style>
                </ResourceDictionary>
            </StackLayout.Resources>
            <Entry Text="These entries" />
            ...
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

Dans cet exemple, l' [`Style`](xref:Xamarin.Forms.Style) *implicite* est assignée à la collection [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) du contrôle [`StackLayout`](xref:Xamarin.Forms.StackLayout) . Le style *implicite* peut ensuite être appliqué au contrôle et à ses enfants.

Pour plus d’informations sur la création de styles dans le [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)d’une application, consultez [styles globaux](~/xamarin-forms/user-interface/styles/application.md).

## <a name="create-an-implicit-style-in-c35"></a>Créer un style implicite en C&#35;

[`Style`](xref:Xamarin.Forms.Style) instances peuvent être ajoutées à la collection de [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) d’une C# page dans en créant un nouveau [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary), puis en ajoutant les instances `Style` à la `ResourceDictionary`, comme illustré dans l’exemple de code suivant :

```csharp
public class ImplicitStylesPageCS : ContentPage
{
    public ImplicitStylesPageCS ()
    {
        var entryStyle = new Style (typeof(Entry)) {
            Setters = {
                ...
                new Setter { Property = Entry.TextColorProperty, Value = Color.Blue }
            }
        };

        ...
        Resources = new ResourceDictionary ();
        Resources.Add (entryStyle);

        Content = new StackLayout {
            Children = {
                new Entry { Text = "These entries" },
                new Entry { Text = "are demonstrating" },
                new Entry { Text = "implicit styles," },
                new Entry { Text = "and an implicit style override", BackgroundColor = Color.Lime, TextColor = Color.Red },
                new CustomEntry  { Text = "Subclassed Entry is not receiving the style" }
            }
        };
    }
}
```

Le constructeur définit un style *implicite* unique qui est appliqué aux instances de [`Entry`](xref:Xamarin.Forms.Entry) de la page. Le `Style` est utilisé pour afficher du texte en bleu sur un arrière-plan jaune, tout en définissant d’autres options d’apparence. Le `Style` est ajouté au [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) de la page sans spécifier une chaîne de `key`. Par conséquent, le `Style` est appliqué à toutes les instances de `Entry` implicitement, car elles correspondent exactement à la propriété [`TargetType`](xref:Xamarin.Forms.Style.TargetType) du `Style`. Toutefois, le `Style` n’est pas appliqué à l’instance de `CustomEntry`, qui est une `Entry`sous-classée.

## <a name="apply-a-style-to-derived-types"></a>Appliquer un style à des types dérivés

La propriété [`Style.ApplyToDerivedTypes`](xref:Xamarin.Forms.Style.ApplyToDerivedTypes) permet d’appliquer un style aux contrôles dérivés du type de base référencé par la propriété [`TargetType`](xref:Xamarin.Forms.Style.TargetType) . Par conséquent, l’affectation de la valeur à cette propriété `true` permet à un seul style de cibler plusieurs types, à condition que les types dérivent du type de base spécifié dans la propriété `TargetType`.

L’exemple suivant montre un style implicite qui définit la couleur d’arrière-plan de [`Button`](xref:Xamarin.Forms.Button) instances sur rouge :

```xaml
<Style TargetType="Button"
       ApplyToDerivedTypes="True">
    <Setter Property="BackgroundColor"
            Value="Red" />
</Style>
```

Le fait de placer ce style dans un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) au niveau de la page entraîne son application à toutes les instances de [`Button`](xref:Xamarin.Forms.Button) de la page, ainsi qu’à tous les contrôles qui dérivent de `Button`. Toutefois, si la propriété [`ApplyToDerivedTypes`](xref:Xamarin.Forms.Style.ApplyToDerivedTypes) est restée non définie, le style est appliqué uniquement aux instances de `Button`.

Le code C# équivalent est :

```csharp
var buttonStyle = new Style(typeof(Button))
{
    ApplyToDerivedTypes = true,
    Setters =
    {
        new Setter
        {
            Property = VisualElement.BackgroundColorProperty,
            Value = Color.Red
        }
    }
};

Resources = new ResourceDictionary { buttonStyle };
```

## <a name="related-links"></a>Liens connexes

- [Extensions de balisage XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Styles de base (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)
- [Utilisation des styles (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
- [Style](xref:Xamarin.Forms.Style)
- [Déclaré](xref:Xamarin.Forms.Setter)
