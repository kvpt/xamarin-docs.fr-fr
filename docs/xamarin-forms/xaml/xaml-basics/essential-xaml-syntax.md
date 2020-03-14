---
title: Partie 2. Syntaxe XAML essentielle
description: Cet article décrit les fonctionnalités de syntaxe XAML essentielles des éléments de propriété et des propriétés jointes.
ms.prod: xamarin
ms.assetid: 4022F1DC-3802-4635-A553-688ABD3F0D5A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/25/2017
ms.openlocfilehash: f79a07a04eddeea1441f7938fdef210a37fb920a
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79305492"
---
# <a name="part-2-essential-xaml-syntax"></a>Partie 2. Syntaxe XAML essentielle

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)

_XAML est principalement conçu pour instancier et initialiser des objets. Mais souvent, les propriétés doivent être définies sur des objets complexes qui ne peuvent pas être facilement représentés en tant que chaînes XML, et parfois les propriétés définies par une classe doivent être définies sur une classe enfant. Ces deux besoins nécessitent les fonctionnalités de syntaxe XAML essentielles des éléments de propriété et des propriétés jointes._

## <a name="property-elements"></a>Éléments de propriété

Dans XAML, propriétés de classes sont normalement définies comme des attributs XML :

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large"
       TextColor="Aqua" />
```

Toutefois, il est une autre façon de définir une propriété dans XAML. Pour tester cette alternative avec `TextColor`, supprimez d’abord le paramètre de `TextColor` existant :

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large" />
```

Ouvrez la balise d' `Label` d’élément vide en la séparant par des balises de début et de fin :

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

Ces deux façons de spécifier la propriété `TextColor` sont fonctionnellement équivalentes, mais n’utilisent pas les deux façons pour la même propriété, car cela permet de définir deux fois la propriété et peut être ambigu.

Avec cette nouvelle syntaxe, définit la terminologie pratique peut être introduite :

- `Label` est un *élément objet*. C’est un objet Xamarin.Forms exprimée sous la forme d’un élément XML.
- `Text`, `VerticalOptions`, `FontAttributes` et `FontSize` sont des *attributs de propriété*. Elles sont Xamarin.Forms exprimée sous la forme d’attributs XML.
- Dans cet extrait de code final, `TextColor` est devenu un *élément de propriété*. C’est une propriété de Xamarin.Forms, mais il est désormais un élément XML.

La définition de propriété éléments peuvent à tout d’abord sembler comme une violation de la syntaxe XML, mais il n’est pas. La période n’a aucune signification spéciale dans XML. Pour un décodeur XML, `Label.TextColor` est simplement un élément enfant normal.

Dans XAML, toutefois, cette syntaxe est très spéciale. L’une des règles pour les éléments de propriété est que rien d’autre ne peut apparaître dans la balise `Label.TextColor`. La valeur de la propriété est toujours définie en tant que contenu entre les balises de fin et le début de l’élément de propriété.

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

Toutefois, la syntaxe d’élément de propriété devient essentiel lorsque la valeur d’une propriété est trop complexe pour être exprimées sous forme de chaîne simple. Dans les balises d’élément de propriété, vous pouvez instancier un autre objet et définir ses propriétés. Par exemple, vous pouvez définir explicitement une propriété telle que `VerticalOptions` sur une valeur de `LayoutOptions` avec les paramètres de propriété :

```xaml
<Label>
    ...
    <Label.VerticalOptions>
        <LayoutOptions Alignment="Center" />
    </Label.VerticalOptions>
</Label>
```

Autre exemple : le `Grid` a deux propriétés nommées `RowDefinitions` et `ColumnDefinitions`. Ces deux propriétés sont de type `RowDefinitionCollection` et `ColumnDefinitionCollection`, qui sont des collections d’objets `RowDefinition` et `ColumnDefinition`. Vous devez utiliser la syntaxe d’élément de propriété pour définir ces collections.

Voici le début du fichier XAML pour une classe `GridDemoPage`, en présentant les balises des éléments de propriété pour les collections `RowDefinitions` et `ColumnDefinitions` :

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

Vous venez de voir que l' `Grid` nécessite des éléments de propriété pour les collections `RowDefinitions` et `ColumnDefinitions` pour définir les lignes et les colonnes. Toutefois, il doit également y avoir un moyen pour le programmeur d’indiquer la ligne et la colonne où chaque enfant du `Grid` réside.

Dans la balise pour chaque enfant de l' `Grid` vous spécifiez la ligne et la colonne de cet enfant à l’aide des attributs suivants :

- `Grid.Row`
- `Grid.Column`

Les valeurs par défaut de ces attributs sont 0. Vous pouvez également indiquer si un enfant s’étend sur plus d’une ligne ou une colonne avec ces attributs :

- `Grid.RowSpan`
- `Grid.ColumnSpan`

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

Les paramètres `Grid.Row` et `Grid.Column` de 0 ne sont pas obligatoires, mais sont généralement inclus à des fins de clarté.

Voici à quoi elle ressemble :

[Disposition de la grille ![](essential-xaml-syntax-images/griddemo.png)](essential-xaml-syntax-images/griddemo-large.png#lightbox)

En cas de jugement uniquement à partir de la syntaxe, ces attributs `Grid.Row`, `Grid.Column`, `Grid.RowSpan`et `Grid.ColumnSpan` semblent être des champs ou des propriétés statiques de `Grid`, mais intéressantment, `Grid` ne définit aucune valeur nommée `Row`, `Column`, `RowSpan`ou `ColumnSpan`.

Au lieu de cela, `Grid` définit quatre propriétés pouvant être liées nommées `RowProperty`, `ColumnProperty`, `RowSpanProperty`et `ColumnSpanProperty`. Il s’agit de types spéciaux de propriétés pouvant être liées, appelées *propriétés jointes*. Ils sont définis par la classe `Grid`, mais définis sur les enfants de l' `Grid`.

Lorsque vous souhaitez utiliser ces propriétés jointes dans le code, la classe `Grid` fournit des méthodes statiques nommées `SetRow`, `GetColumn`et ainsi de suite. En XAML, ces propriétés jointes sont définies en tant qu’attributs dans les enfants de l' `Grid` à l’aide de noms de propriétés simples.

Propriétés jointes sont toujours reconnaissables dans les fichiers XAML en tant qu’attributs contenant une classe et un nom de propriété séparés par un point. Ils sont appelés *propriétés jointes* , car ils sont définis par une classe (dans ce cas, `Grid`), mais attachés à d’autres objets (dans ce cas, les enfants de l' `Grid`). Pendant la disposition, l' `Grid` peut interroger les valeurs de ces propriétés jointes pour savoir où placer chaque enfant.

La classe `AbsoluteLayout` définit deux propriétés jointes nommées `LayoutBounds` et `LayoutFlags`. Voici un modèle de damier réalisé à l’aide des fonctionnalités de positionnement et de dimensionnement proportionnels de `AbsoluteLayout`:

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

[Disposition ![absolue](essential-xaml-syntax-images/absolutedemo-large.png)](essential-xaml-syntax-images/absolutedemo-large.png#lightbox)

Quelque chose comme ceci, vous pourrez remettre en question la sagesse d’à l’aide de XAML. Certainement, la répétition et la régularité du rectangle `LayoutBounds` suggèrent qu’il peut être mieux réalisé dans le code.

Qui certainement inquiétude est légitime, et il n’existe aucun problème avec l’équilibrage de l’utilisation de code et le balisage lors de la définition de vos interfaces utilisateur. Il est facile définir certains éléments visuels dans XAML, puis utiliser le constructeur du fichier code-behind pour ajouter certains éléments visuels plus susceptibles d’être mieux générées dans des boucles.

## <a name="content-properties"></a>Propriétés de contenu

Dans les exemples précédents, les objets `StackLayout`, `Grid`et `AbsoluteLayout` sont définis sur la propriété `Content` du `ContentPage`, et les enfants de ces dispositions sont en fait des éléments de la collection `Children`. Toutefois, ces propriétés `Content` et `Children` ne sont pas dans le fichier XAML.

Vous pouvez certainement inclure les propriétés `Content` et `Children` en tant qu’éléments de propriété, comme dans l’exemple **XamlPlusCode** :

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

La question réelle est : pourquoi ces éléments de propriété *ne* sont-ils pas requis dans le fichier XAML ?

Les éléments définis dans Xamarin. Forms pour une utilisation en XAML sont autorisés à avoir une propriété signalée dans l’attribut `ContentProperty` de la classe. Si vous recherchez la classe `ContentPage` dans la documentation en ligne de Xamarin. Forms, vous verrez cet attribut :

```csharp
[Xamarin.Forms.ContentProperty("Content")]
public class ContentPage : TemplatedPage
```

Cela signifie que les balises d’éléments de propriété `Content` ne sont pas requises. Tout contenu XML qui apparaît entre les balises de début et de fin `ContentPage` est supposé être assigné à la propriété `Content`.

 `StackLayout`, `Grid`, `AbsoluteLayout`et `RelativeLayout` dérivent tous de `Layout<View>`, et si vous recherchez `Layout<T>` dans la documentation Xamarin. Forms, vous verrez un autre attribut `ContentProperty` :

```csharp
[Xamarin.Forms.ContentProperty("Children")]
public abstract class Layout<T> : Layout ...
```

Qui autorise l’ajout automatique du contenu de la disposition à la collection `Children` sans balises d’éléments de propriété `Children` explicites.

D’autres classes possèdent également des définitions d’attributs `ContentProperty`. Par exemple, la propriété de contenu de `Label` est `Text`. Consultez la documentation API pour d’autres.

## <a name="platform-differences-with-onplatform"></a>Différences de plateforme avec OnPlatform

Dans les applications à page unique, il est courant de définir la propriété `Padding` sur la page afin d’éviter de remplacer la barre d’État iOS. Dans le code, vous pouvez utiliser la propriété `Device.RuntimePlatform` à cet effet :

```csharp
if (Device.RuntimePlatform == Device.iOS)
{
    Padding = new Thickness(0, 20, 0, 0);
}
```

Vous pouvez également effectuer une opération similaire en XAML à l’aide des classes [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1) et [`On`](xref:Xamarin.Forms.On) . Tout d’abord, incluez les éléments de propriété pour la propriété `Padding` près du haut de la page :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>

    </ContentPage.Padding>
    ...
</ContentPage>
```

Au sein de ces balises, incluez une balise `OnPlatform`. `OnPlatform` est une classe générique. Vous devez spécifier l’argument de type générique, dans ce cas, `Thickness`, qui est le type de `Padding` propriété. Heureusement, il existe un attribut XAML spécifiquement pour définir des arguments génériques appelés `x:TypeArguments`. Cela doit correspondre au type de la propriété que vous définissez :

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

`OnPlatform` a une propriété nommée `Platforms` qui est un `IList` d' `On` objets. Utiliser des balises d’élément de propriété pour cette propriété :

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

À présent, ajoutez des éléments `On`. Pour chacune d’elles, définissez la propriété `Platform` et la propriété `Value` sur le balisage de la propriété `Thickness` :

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

Ce balisage peut être simplifié. La propriété de contenu de `OnPlatform` est `Platforms`. par conséquent, ces balises d’élément de propriété peuvent être supprimées :

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

La propriété `Platform` de `On` étant de type `IList<string>`, vous pouvez inclure plusieurs plateformes si les valeurs sont les mêmes :

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

Comme Android et UWP sont définis sur la valeur par défaut de `Padding`, cette balise peut être supprimée :

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

Il s’agit de la méthode standard pour définir une propriété de `Padding` dépendante de la plateforme en XAML. Si le paramètre `Value` ne peut pas être représenté par une chaîne unique, vous pouvez définir des éléments de propriété pour celui-ci :

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
> L’extension de balisage `OnPlatform` peut également être utilisée en XAML pour personnaliser l’apparence de l’interface utilisateur sur la base de chaque plateforme. Il fournit les mêmes fonctionnalités que les classes `OnPlatform` et `On`, mais avec une représentation plus concise. Pour plus d’informations, consultez [OnPlatform Markup Extension](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform).

## <a name="summary"></a>Résumé

Avec les éléments de propriété et les propriétés jointes, une grande partie de la syntaxe XAML de base a été établie. Toutefois, vous devez parfois définir des propriétés aux objets de manière indirecte, par exemple, à partir d’un dictionnaire de ressources. Cette approche est traitée dans la partie suivante, partie [3. Extensions de balisage XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md).

## <a name="related-links"></a>Liens connexes

- [XamlSamples](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)
- [Partie 1. Prise en main avec XAML](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [Partie 3. Extensions de balisage XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Partie 4. Principes fondamentaux de la liaison de données](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [Partie 5. De la liaison de données au MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
