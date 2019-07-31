---
title: Partie 2. Syntaxe XAML essentielle
description: Cet article décrit les fonctionnalités de syntaxe XAML essentielles des éléments de propriété et des propriétés jointes.
ms.prod: xamarin
ms.assetid: 4022F1DC-3802-4635-A553-688ABD3F0D5A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/25/2017
ms.openlocfilehash: d8052e00809b15f0858583ee2919c47cfd8af00b
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68646569"
---
# <a name="part-2-essential-xaml-syntax"></a>Partie 2. Syntaxe XAML essentielle

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)

_XAML est principalement conçu pour l’instanciation et initialisation d’objets. Mais souvent, propriétés doivent être définies pour des objets complexes qui ne peuvent pas facilement être représentées sous forme de chaînes XML, et parfois, les propriétés définies par une classe doivent être définies sur une classe enfant. Ces deux besoins nécessitent les fonctionnalités de syntaxe XAML essentielles des éléments de propriété et les propriétés jointes._

## <a name="property-elements"></a>Éléments de propriété

Dans XAML, propriétés de classes sont normalement définies comme des attributs XML :

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large"
       TextColor="Aqua" />
```

Toutefois, il est une autre façon de définir une propriété dans XAML. Pour essayer cette solution avec `TextColor`, supprimez d’abord existant `TextColor` paramètre :

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large" />
```

Ouvrez l’élément de vide `Label` balise par en le séparant dans les balises de début et de fin :

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large">

</Label>
```

Dans ces balises, ajouter des balises de début et de fin qui comprennent le nom de classe et un nom de propriété séparés par un point :

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large">
    <Label.TextColor>

    </Label.TextColor>
</Label>
```

Définissez la valeur de propriété en tant que contenu de ces nouvelles balises, comme suit :

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large">
    <Label.TextColor>
        Aqua
    </Label.TextColor>
</Label>
```

Ces deux façons de spécifier le `TextColor` propriété sont fonctionnellement équivalents, mais n’utilisez pas les deux méthodes pour la même propriété, car qui serait effectivement être définissant la propriété à deux reprises et peuvent être ambigus.

Avec cette nouvelle syntaxe, définit la terminologie pratique peut être introduite :

-  `Label` est un *élément objet*. C’est un objet Xamarin.Forms exprimée sous la forme d’un élément XML.
-  `Text`, `VerticalOptions`, `FontAttributes` et `FontSize` sont *les attributs de propriété*. Elles sont Xamarin.Forms exprimée sous la forme d’attributs XML.
-  Dans cet extrait de code final, `TextColor` est devenu un *élément property*. C’est une propriété de Xamarin.Forms, mais il est désormais un élément XML.


La définition de propriété éléments peuvent à tout d’abord sembler comme une violation de la syntaxe XML, mais il n’est pas. La période n’a aucune signification spéciale dans XML. Pour un décodeur XML, `Label.TextColor` est simplement un élément enfant normal.

Dans XAML, toutefois, cette syntaxe est très spéciale. Une des règles pour les éléments de propriété est que rien d’autre peut apparaître dans le `Label.TextColor` balise. La valeur de la propriété est toujours définie en tant que contenu entre les balises de fin et le début de l’élément de propriété.

Vous pouvez utiliser la syntaxe d’élément de propriété sur plusieurs propriétés :

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center">
    <Label.FontAttributes>
        Bold
    </Label.FontAttributes>
    <Label.FontSize>
        Large
    </Label.FontSize>
    <Label.TextColor>
        Aqua
    </Label.TextColor>
</Label>
```

Ou vous pouvez utiliser la syntaxe d’élément de propriété pour toutes les propriétés :

```xaml
<Label>
    <Label.Text>
        Hello, XAML!
    </Label.Text>
    <Label.FontAttributes>
        Bold
    </Label.FontAttributes>
    <Label.FontSize>
        Large
    </Label.FontSize>
    <Label.TextColor>
        Aqua
    </Label.TextColor>
    <Label.VerticalOptions>
        Center
    </Label.VerticalOptions>
</Label>
```

Dans un premier temps, syntaxe d’élément de propriété peut sembler un remplacement plus détaillée inutile pour quelque chose de relativement simple, et dans ces exemples qui n’est certainement le cas.

Toutefois, la syntaxe d’élément de propriété devient essentiel lorsque la valeur d’une propriété est trop complexe pour être exprimées sous forme de chaîne simple. Dans les balises d’élément de propriété, vous pouvez instancier un autre objet et définir ses propriétés. Par exemple, vous pouvez définir explicitement une propriété comme `VerticalOptions` à un `LayoutOptions` valeur avec les paramètres de propriété :

```xaml
<Label>
    ...
    <Label.VerticalOptions>
        <LayoutOptions Alignment="Center" />
    </Label.VerticalOptions>
</Label>
```

Autre exemple: A deux propriétés nommées `RowDefinitions` et `ColumnDefinitions`. `Grid` Ces deux propriétés sont de type `RowDefinitionCollection` et `ColumnDefinitionCollection`, qui sont des collections de `RowDefinition` et `ColumnDefinition` objets. Vous devez utiliser la syntaxe d’élément de propriété pour définir ces collections.

Voici le début du fichier XAML pour un `GridDemoPage` (classe), montrant les balises d’élément de propriété pour le `RowDefinitions` et `ColumnDefinitions` collections :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.GridDemoPage"
             Title="Grid Demo Page">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
            <RowDefinition Height="100" />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="Auto" />
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="100" />
        </Grid.ColumnDefinitions>
        ...
    </Grid>
</ContentPage>
```

Notez la syntaxe abrégée pour définir dimensionnée automatiquement des cellules, de pixel largeurs et hauteurs et les paramètres en étoile.

## <a name="attached-properties"></a>Propriétés jointes

Vous venez de voir que le `Grid` nécessite les éléments de propriété pour le `RowDefinitions` et `ColumnDefinitions` regroupements pour définir les lignes et colonnes. Toutefois, il doit également être un moyen pour le programmeur indiquer la ligne et colonne où chaque enfant de le `Grid` réside.

Dans la balise pour chaque enfant de le `Grid` vous spécifiez la ligne et colonne de cet enfant à l’aide des attributs suivants :

-  `Grid.Row`
-  `Grid.Column`

Les valeurs par défaut de ces attributs sont 0. Vous pouvez également indiquer si un enfant s’étend sur plus d’une ligne ou une colonne avec ces attributs :

-  `Grid.RowSpan`
-  `Grid.ColumnSpan`

Ces deux attributs possèdent des valeurs par défaut de 1.

Voici le fichier GridDemoPage.xaml complet :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.GridDemoPage"
             Title="Grid Demo Page">

    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
            <RowDefinition Height="100" />
        </Grid.RowDefinitions>

        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="Auto" />
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="100" />
        </Grid.ColumnDefinitions>

        <Label Text="Autosized cell"
               Grid.Row="0" Grid.Column="0"
               TextColor="White"
               BackgroundColor="Blue" />

        <BoxView Color="Silver"
                 HeightRequest="0"
                 Grid.Row="0" Grid.Column="1" />

        <BoxView Color="Teal"
                 Grid.Row="1" Grid.Column="0" />

        <Label Text="Leftover space"
               Grid.Row="1" Grid.Column="1"
               TextColor="Purple"
               BackgroundColor="Aqua"
               HorizontalTextAlignment="Center"
               VerticalTextAlignment="Center" />

        <Label Text="Span two rows (or more if you want)"
               Grid.Row="0" Grid.Column="2" Grid.RowSpan="2"
               TextColor="Yellow"
               BackgroundColor="Blue"
               HorizontalTextAlignment="Center"
               VerticalTextAlignment="Center" />

        <Label Text="Span two columns"
               Grid.Row="2" Grid.Column="0" Grid.ColumnSpan="2"
               TextColor="Blue"
               BackgroundColor="Yellow"
               HorizontalTextAlignment="Center"
               VerticalTextAlignment="Center" />

        <Label Text="Fixed 100x100"
               Grid.Row="2" Grid.Column="2"
               TextColor="Aqua"
               BackgroundColor="Red"
               HorizontalTextAlignment="Center"
               VerticalTextAlignment="Center" />

    </Grid>
</ContentPage>
```

Le `Grid.Row` et `Grid.Column` paramètres 0 ne sont pas nécessaires mais sont généralement inclus pour des raisons de clarté.

Voici à quoi elle ressemble :

[![](essential-xaml-syntax-images/griddemo.png "Disposition en grille")](essential-xaml-syntax-images/griddemo-large.png#lightbox "mise en page de grille")

Juger uniquement à partir de la syntaxe, ces `Grid.Row`, `Grid.Column`, `Grid.RowSpan`, et `Grid.ColumnSpan` attributs semblent être des champs statiques ou des propriétés de `Grid`, mais il est intéressant, `Grid` ne définit aucun élément nommé `Row`, `Column`, `RowSpan`, ou `ColumnSpan`.

Au lieu de cela, `Grid` définit quatre propriétés pouvant être liées nommées `RowProperty`, `ColumnProperty`, `RowSpanProperty`, et `ColumnSpanProperty`. Il s’agit des types spéciaux de propriétés pouvant être liées, connu sous le nom *propriétés jointes*. Elles sont définies par le `Grid` classe mais définis sur les enfants de le `Grid`.

Lorsque vous souhaitez utiliser ces propriétés jointes dans le code, le `Grid` classe fournit des méthodes statiques nommés `SetRow`, `GetColumn`, et ainsi de suite. Mais dans XAML, ces propriétés jointes sont définies en tant qu’attributs dans les enfants de le `Grid` à l’aide de noms de propriétés simples.

Propriétés jointes sont toujours reconnaissables dans les fichiers XAML en tant qu’attributs contenant une classe et un nom de propriété séparés par un point. Elles sont appelées *propriétés jointes* , car elles sont définies par une classe (dans ce cas, `Grid`) mais est associé à d’autres objets (dans ce cas, les enfants de le `Grid`). Pendant la disposition, le `Grid` peuvent interroger les valeurs de ces propriétés jointes de savoir où placer chaque enfant.

Le `AbsoluteLayout` classe définit deux propriétés jointes nommées `LayoutBounds` et `LayoutFlags`. Voici un modèle de damier réalisé à l’aide de la zone de positionnement proportionnelle et les fonctionnalités de dimensionnement de `AbsoluteLayout`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.AbsoluteDemoPage"
             Title="Absolute Demo Page">

    <AbsoluteLayout BackgroundColor="#FF8080">
        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="0.33, 0, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="1, 0, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="0, 0.33, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="0.67, 0.33, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="0.33, 0.67, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="1, 0.67, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="0, 1, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="0.67, 1, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

  </AbsoluteLayout>
</ContentPage>
```

Et voici :

[![](essential-xaml-syntax-images/absolutedemo-large.png "Disposition absolue")](essential-xaml-syntax-images/absolutedemo-large.png#lightbox "disposition absolue")

Quelque chose comme ceci, vous pourrez remettre en question la sagesse d’à l’aide de XAML. Sans aucun doute, la répétition et la régularité de la `LayoutBounds` rectangle suggère qu’il peut être mieux a réalisé dans le code.

Qui certainement inquiétude est légitime, et il n’existe aucun problème avec l’équilibrage de l’utilisation de code et le balisage lors de la définition de vos interfaces utilisateur. Il est facile définir certains éléments visuels dans XAML, puis utiliser le constructeur du fichier code-behind pour ajouter certains éléments visuels plus susceptibles d’être mieux générées dans des boucles.

## <a name="content-properties"></a>Propriétés de contenu

Dans les exemples précédents, le `StackLayout`, `Grid`, et `AbsoluteLayout` objets sont définis sur le `Content` propriété de la `ContentPage`, et les enfants de ces dispositions sont réellement les éléments dans le `Children` collection. Mais ces `Content` et `Children` propriétés sont nulle part dans le fichier XAML.

Vous pouvez inclure certainement le `Content` et `Children` propriétés en tant qu’éléments de propriété, comme dans le **XamlPlusCode** exemple :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.XamlPlusCodePage"
             Title="XAML + Code Page">
    <ContentPage.Content>
        <StackLayout>
            <StackLayout.Children>
                <Slider VerticalOptions="CenterAndExpand"
                        ValueChanged="OnSliderValueChanged" />

                <Label x:Name="valueLabel"
                       Text="A simple Label"
                       FontSize="Large"
                       HorizontalOptions="Center"
                       VerticalOptions="CenterAndExpand" />

                <Button Text="Click Me!"
                      HorizontalOptions="Center"
                      VerticalOptions="CenterAndExpand"
                      Clicked="OnButtonClicked" />
            </StackLayout.Children>
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

La question réelle est la suivante: Pourquoi ces éléments de propriété *ne* sont-ils pas requis dans le fichier XAML?

Les éléments définis dans Xamarin.Forms pour une utilisation dans XAML peuvent avoir une propriété signalée dans le `ContentProperty` attribut sur la classe. Si vous recherchez la `ContentPage` classe dans la documentation en ligne de Xamarin.Forms, vous verrez cet attribut :

```csharp
[Xamarin.Forms.ContentProperty("Content")]
public class ContentPage : TemplatedPage
```

Cela signifie que le `Content` balises d’élément de propriété ne sont pas requis. Tout contenu XML qui s’affiche entre le début et de fin `ContentPage` balises est censé être affectée à la `Content` propriété.

 `StackLayout`, `Grid`, `AbsoluteLayout`, et `RelativeLayout` dérivent toutes de `Layout<View>`, et si vous recherchez `Layout<T>` dans la documentation de Xamarin.Forms, vous verrez un autre `ContentProperty` attribut :

```csharp
[Xamarin.Forms.ContentProperty("Children")]
public abstract class Layout<T> : Layout ...
```

Qui permet au contenu de la disposition soient ajoutés automatiquement à la `Children` collection sans explicite `Children` balises d’élément de propriété.

Autres classes ont également `ContentProperty` des définitions d’attributs. Par exemple, la propriété de contenu de `Label` est `Text`. Consultez la documentation API pour d’autres.

## <a name="platform-differences-with-onplatform"></a>Différences de plateforme avec OnPlatform

Dans les applications à page unique, il est courant pour définir le `Padding` propriété sur la page pour éviter le remplacement de la barre d’état iOS. Dans le code, vous pouvez utiliser le `Device.RuntimePlatform` propriété à cet effet :

```csharp
if (Device.RuntimePlatform == Device.iOS)
{
    Padding = new Thickness(0, 20, 0, 0);
}
```

Vous pouvez également effectuer une opération similaire en XAML à [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1) l' [`On`](xref:Xamarin.Forms.On) aide des classes et. Tout d’abord inclure des éléments de propriété pour le `Padding` propriété vers le haut de la page :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>

    </ContentPage.Padding>
    ...
</ContentPage>
```

Dans ces balises, vous devez inclure un `OnPlatform` balise. `OnPlatform` est une classe générique. Vous devez spécifier l’argument de type générique, dans ce cas, `Thickness`, qui est le type de `Padding` propriété. Heureusement, il existe un attribut XAML spécifiquement pour définir des arguments génériques appelés `x:TypeArguments`. Cela doit correspondre au type de la propriété que vous définissez :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">

        </OnPlatform>
    </ContentPage.Padding>
  ...
</ContentPage>
```

`OnPlatform` a une propriété nommée `Platforms` autrement dit un `IList` de `On` objets. Utiliser des balises d’élément de propriété pour cette propriété :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <OnPlatform.Platforms>

            </OnPlatform.Platforms>
        </OnPlatform>
    </ContentPage.Padding>
  ...
</ContentPage>
```

Ajoutez maintenant `On` éléments. Pour chacun d'entre eux, définissez la `Platform` propriété et la `Value` propriété balisage pour le `Thickness` propriété :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <OnPlatform.Platforms>
                <On Platform="iOS" Value="0, 20, 0, 0" />
                <On Platform="Android" Value="0, 0, 0, 0" />
                <On Platform="UWP" Value="0, 0, 0, 0" />
            </OnPlatform.Platforms>
        </OnPlatform>
    </ContentPage.Padding>
  ...
</ContentPage>
```

Ce balisage peut être simplifié. La propriété content de `OnPlatform` est `Platforms`, de sorte que ces balises d’élément de propriété peuvent être supprimés :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="iOS" Value="0, 20, 0, 0" />
            <On Platform="Android" Value="0, 0, 0, 0" />
            <On Platform="UWP" Value="0, 0, 0, 0" />
        </OnPlatform>
    </ContentPage.Padding>
  ...
</ContentPage>
```

Le `Platform` propriété du `On` est de type `IList<string>`, de sorte que si les valeurs sont identiques, vous pouvez inclure plusieurs plateformes :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="iOS" Value="0, 20, 0, 0" />
            <On Platform="Android, UWP" Value="0, 0, 0, 0" />
        </OnPlatform>
    </ContentPage.Padding>
  ...
</ContentPage>
```

Étant donné que Android et UWP sont définies sur la valeur par défaut `Padding`, que la balise peut être supprimée :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="iOS" Value="0, 20, 0, 0" />
        </OnPlatform>
    </ContentPage.Padding>
  ...
</ContentPage>
```

C’est le moyen standard pour définir un dépend de la plateforme `Padding` propriété dans XAML. Si le `Value` paramètre ne peut pas être représenté par une chaîne unique, vous pouvez définir des éléments de propriété pour lui :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="iOS">
                <On.Value>
                    0, 20, 0, 0
                </On.Value>
            </On>
        </OnPlatform>
    </ContentPage.Padding>
  ...
</ContentPage>
```

> [!NOTE]
> L' `OnPlatform` extension de balisage peut également être utilisée en XAML pour personnaliser l’apparence de l’interface utilisateur pour chaque plateforme. Il fournit les mêmes fonctionnalités que les `OnPlatform` classes `On` et, mais avec une représentation plus concise. Pour plus d’informations, consultez [Extension de balisage OnPlatform](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform).

## <a name="summary"></a>Récapitulatif

Avec les éléments de propriété et les propriétés jointes, une grande partie de la syntaxe XAML de base a été établie. Toutefois, vous devez parfois définir des propriétés aux objets de manière indirecte, par exemple, à partir d’un dictionnaire de ressources. Cette approche est abordée dans la partie suivante, la partie [3. Extensions de balisage XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md).

## <a name="related-links"></a>Liens associés

- [XamlSamples](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)
- [Partie 1. Bien démarrer avec XAML](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [Partie 3. Extensions de balisage XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Partie 4. Notions de base sur la liaison de données](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [Partie 5. À partir de la liaison de données à MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
