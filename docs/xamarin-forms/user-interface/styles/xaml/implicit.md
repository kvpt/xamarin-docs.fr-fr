---
title: Styles implicites dans Xamarin.Forms
description: Un style implicite est un style qui est utilisé par tous les contrôles du même TargetType, sans qu’il soit nécessaire que chaque contrôle référence le style.
ms.prod: xamarin
ms.assetid: 02A75F3B-4389-49D4-A2F4-AFD473A4A161
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/30/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: ad297b735449796925b92fbc9d1887db9e8a1c99
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563326"
---
# <a name="implicit-styles-in-no-locxamarinforms"></a>Styles implicites dans Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)

_Un style implicite est un style qui est utilisé par tous les contrôles du même TargetType, sans qu’il soit nécessaire que chaque contrôle référence le style._

## <a name="create-an-implicit-style-in-xaml"></a>Créer un style implicite en XAML

Pour déclarer un [`Style`](xref:Xamarin.Forms.Style) au niveau de la page, un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) doit être ajouté à la page, puis une ou plusieurs `Style` déclarations peuvent être incluses dans le `ResourceDictionary` . Un `Style` est rendu *implicite* en ne spécifiant pas d' `x:Key` attribut. Le style est ensuite appliqué aux éléments visuels qui correspondent `TargetType` exactement, mais pas aux éléments dérivés de la `TargetType` valeur.

L’exemple de code suivant montre un style *implicite* déclaré en XAML dans une page `ResourceDictionary` et appliqué aux instances de la page [`Entry`](xref:Xamarin.Forms.Entry) :

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

Le [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) définit un seul style *implicite* qui est appliqué aux instances de la page [`Entry`](xref:Xamarin.Forms.Entry) . `Style`Est utilisé pour afficher du texte bleu sur un arrière-plan jaune, tout en définissant d’autres options d’apparence. Le `Style` est ajouté à la page [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) sans spécifier d' `x:Key` attribut. Par conséquent, `Style` est appliqué à toutes les `Entry` instances implicitement, car elles correspondent [`TargetType`](xref:Xamarin.Forms.Style.TargetType) exactement à la propriété du `Style` . Toutefois, le `Style` n’est pas appliqué à l' `CustomEntry` instance, qui est une sous-classe `Entry` . Cela donne l’affichage illustré dans les captures d’écran suivantes :

[![Exemple de styles implicites](implicit-images/implicit-styles.png)](implicit-images/implicit-styles-large.png#lightbox)

En outre, la quatrième [`Entry`](xref:Xamarin.Forms.Entry) remplace les [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) [`TextColor`](xref:Xamarin.Forms.InputView.TextColor) Propriétés et du style implicite par des valeurs différentes `Color` .

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

Dans cet exemple, le *implicite* [`Style`](xref:Xamarin.Forms.Style) est assigné à la [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) collection du [`StackLayout`](xref:Xamarin.Forms.StackLayout) contrôle. Le style *implicite* peut ensuite être appliqué au contrôle et à ses enfants.

Pour plus d’informations sur la création de styles dans une application [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) , consultez [styles globaux](~/xamarin-forms/user-interface/styles/application.md).

## <a name="create-an-implicit-style-in-c35"></a>Créer un style implicite en C&#35;

[`Style`](xref:Xamarin.Forms.Style) les instances peuvent être ajoutées à la collection d’une page [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) en C# en créant un nouveau [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) , puis en ajoutant les `Style` instances à `ResourceDictionary` , comme illustré dans l’exemple de code suivant :

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

Le constructeur définit un seul style *implicite* qui est appliqué aux instances de la page [`Entry`](xref:Xamarin.Forms.Entry) . `Style`Est utilisé pour afficher du texte bleu sur un arrière-plan jaune, tout en définissant d’autres options d’apparence. Le `Style` est ajouté à la page [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) sans spécifier de `key` chaîne. Par conséquent, `Style` est appliqué à toutes les `Entry` instances implicitement, car elles correspondent [`TargetType`](xref:Xamarin.Forms.Style.TargetType) exactement à la propriété du `Style` . Toutefois, le `Style` n’est pas appliqué à l' `CustomEntry` instance, qui est une sous-classe `Entry` .

## <a name="apply-a-style-to-derived-types"></a>Appliquer un style à des types dérivés

La [`Style.ApplyToDerivedTypes`](xref:Xamarin.Forms.Style.ApplyToDerivedTypes) propriété permet l’application d’un style aux contrôles dérivés du type de base référencé par la [`TargetType`](xref:Xamarin.Forms.Style.TargetType) propriété. Par conséquent, la définition de cette propriété sur `true` permet à un seul style de cibler plusieurs types, à condition que les types dérivent du type de base spécifié dans la `TargetType` propriété.

L’exemple suivant montre un style implicite qui définit la couleur d’arrière-plan des [`Button`](xref:Xamarin.Forms.Button) instances sur rouge :

```xaml
<Style TargetType="Button"
       ApplyToDerivedTypes="True">
    <Setter Property="BackgroundColor"
            Value="Red" />
</Style>
```

Si vous placez ce style au niveau de [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) la page, il sera appliqué à toutes les [`Button`](xref:Xamarin.Forms.Button) instances de la page, ainsi qu’à tous les contrôles qui dérivent de `Button` . Toutefois, si la [`ApplyToDerivedTypes`](xref:Xamarin.Forms.Style.ApplyToDerivedTypes) propriété est restée non définie, le style est appliqué uniquement aux `Button` instances.

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
- [Styles de base (exemple)](/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)
- [Utilisation des styles (exemple)](/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
- [Style](xref:Xamarin.Forms.Style)
- [Déclaré](xref:Xamarin.Forms.Setter)