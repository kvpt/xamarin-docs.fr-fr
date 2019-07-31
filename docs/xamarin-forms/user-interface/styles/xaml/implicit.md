---
title: Styles implicites dans Xamarin.Forms
description: Un style implicite est celui qui est utilisé par tous les contrôles de la même TargetType, sans nécessiter de chaque contrôle pour faire référence au style.
ms.prod: xamarin
ms.assetid: 02A75F3B-4389-49D4-A2F4-AFD473A4A161
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/30/2019
ms.openlocfilehash: 328063fd6924902738722813cfb961e56af5385e
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68644473"
---
# <a name="implicit-styles-in-xamarinforms"></a>Styles implicites dans Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)

_Un style implicite est celui qui est utilisé par tous les contrôles de la même TargetType, sans nécessiter de chaque contrôle pour faire référence au style._

## <a name="create-an-implicit-style-in-xaml"></a>Créer un style implicite en XAML

Pour déclarer un [ `Style` ](xref:Xamarin.Forms.Style) au niveau de la page, un [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) doit être ajouté à la page et ensuite un ou plusieurs `Style` déclarations peuvent être incluses dans le `ResourceDictionary`. Un `Style` a *implicite* en ne spécifiant un `x:Key` attribut. Le style sera ensuite être appliqué à des éléments visuels qui correspondent à la `TargetType` exactement, mais pas aux éléments qui sont dérivés de la `TargetType` valeur.

Le code suivant montre l’exemple un *implicite* style déclaré dans XAML dans une page `ResourceDictionary`et appliquée à la page [ `Entry` ](xref:Xamarin.Forms.Entry) instances :

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

Le [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) définit un seul *implicite* style est appliqué à la page [ `Entry` ](xref:Xamarin.Forms.Entry) instances. Le `Style` est utilisé pour afficher le texte en bleu sur un arrière-plan jaune, lors de la définition également les autres options d’apparence. Le `Style` est ajoutée à la page [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) sans spécifier un `x:Key` attribut. Par conséquent, le `Style` est appliquée à tous le `Entry` instances implicitement qu’ils correspondent le [ `TargetType` ](xref:Xamarin.Forms.Style.TargetType) propriété de la `Style` exactement. Toutefois, le `Style` n’est pas appliqué à la `CustomEntry` instance, ce qui est une sous-classe `Entry`. Cela provoque l’affichage indiqué dans les captures d’écran suivante :

[![](implicit-images/implicit-styles.png "Exemple de Styles implicites")](implicit-images/implicit-styles-large.png#lightbox "exemple de Styles implicites")

En outre, la quatrième [ `Entry` ](xref:Xamarin.Forms.Entry) remplace le [ `BackgroundColor` ](xref:Xamarin.Forms.VisualElement.BackgroundColor) et [ `TextColor` ](xref:Xamarin.Forms.Entry.TextColor) propriétés du style implicite à différents `Color`valeurs.

### <a name="create-an-implicit-style-at-the-control-level"></a>Créer un style implicite au niveau du contrôle

Outre la création de *implicite* styles au niveau de la page, ils peuvent également être créés au niveau du contrôle, comme indiqué dans l’exemple de code suivant :

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

Dans cet exemple, le *implicite* [ `Style` ](xref:Xamarin.Forms.Style) est affectée à la [ `Resources` ](xref:Xamarin.Forms.VisualElement.Resources) collection de la [ `StackLayout` ](xref:Xamarin.Forms.StackLayout)contrôle. Le *implicite* style peut ensuite être appliqué au contrôle et ses enfants.

Pour plus d’informations sur la création de styles dans une application [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary), consultez [Styles globaux](~/xamarin-forms/user-interface/styles/application.md).

## <a name="create-an-implicit-style-in-c35"></a>Créer un style implicite en C&#35;

[`Style`](xref:Xamarin.Forms.Style) instances peuvent être ajoutées à une page [ `Resources` ](xref:Xamarin.Forms.VisualElement.Resources) collection en C# en créant un nouveau [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)et puis en ajoutant le `Style` instances pour le `ResourceDictionary`, comme illustré dans la exemple de code suivant :

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

Le constructeur définit un seul *implicite* style est appliqué à la page [ `Entry` ](xref:Xamarin.Forms.Entry) instances. Le `Style` est utilisé pour afficher le texte en bleu sur un arrière-plan jaune, lors de la définition également les autres options d’apparence. Le `Style` est ajoutée à la page [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) sans spécifier un `key` chaîne. Par conséquent, le `Style` est appliquée à tous le `Entry` instances implicitement qu’ils correspondent le [ `TargetType` ](xref:Xamarin.Forms.Style.TargetType) propriété de la `Style` exactement. Toutefois, le `Style` n’est pas appliqué à la `CustomEntry` instance, ce qui est une sous-classe `Entry`.

## <a name="apply-a-style-to-derived-types"></a>Appliquer un style à des types dérivés

La [`Style.ApplyToDerivedTypes`](xref:Xamarin.Forms.Style.ApplyToDerivedTypes) propriété permet l’application d’un style aux contrôles dérivés du type de base référencé par la [`TargetType`](xref:Xamarin.Forms.Style.TargetType) propriété. Par conséquent, la définition de `true` cette propriété sur permet à un seul style de cibler plusieurs types, à condition que les types dérivent `TargetType` du type de base spécifié dans la propriété.

L’exemple suivant montre un style implicite qui définit la couleur d' [`Button`](xref:Xamarin.Forms.Button) arrière-plan des instances sur rouge:

```xaml
<Style TargetType="Button"
       ApplyToDerivedTypes="True">
    <Setter Property="BackgroundColor"
            Value="Red" />
</Style>
```

Si vous placez ce style au niveau [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) de la page, il sera appliqué à toutes les [`Button`](xref:Xamarin.Forms.Button) instances de la page, ainsi qu’à tous les contrôles qui `Button`dérivent de. Toutefois, si la [`ApplyToDerivedTypes`](xref:Xamarin.Forms.Style.ApplyToDerivedTypes) propriété est restée non définie, le style est appliqué uniquement `Button` aux instances.

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
- [Utilisation des Styles (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
- [Style](xref:Xamarin.Forms.Style)
- [Setter](xref:Xamarin.Forms.Setter)
