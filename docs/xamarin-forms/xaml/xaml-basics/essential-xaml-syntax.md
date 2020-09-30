---
title: Partie 2. Syntaxe XAML essentielle
description: Cet article explique les fonctionnalités essentielles de la syntaxe XAML des éléments de propriété et des propriétés jointes.
ms.prod: xamarin
ms.assetid: 4022F1DC-3802-4635-A553-688ABD3F0D5A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/25/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e8afb0aa2a36a73af6c63e48a6c1048d8b69cf90
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563976"
---
# <a name="part-2-essential-xaml-syntax"></a>Partie 2. Syntaxe XAML essentielle

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)

_XAML est principalement conçu pour instancier et initialiser des objets. Mais souvent, les propriétés doivent être définies sur des objets complexes qui ne peuvent pas être facilement représentés en tant que chaînes XML, et parfois les propriétés définies par une classe doivent être définies sur une classe enfant. Ces deux besoins nécessitent les fonctionnalités de syntaxe XAML essentielles des éléments de propriété et des propriétés jointes._

## <a name="property-elements"></a>Éléments de propriété

En XAML, les propriétés des classes sont normalement définies en tant qu’attributs XML :

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large"
       TextColor="Aqua" />
```

Toutefois, il existe un autre moyen de définir une propriété en XAML. Pour tester cette alternative avec `TextColor` , commencez par supprimer le `TextColor` paramètre existant :

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large" />
```

Ouvrez la `Label` balise d’élément vide en la séparant par des balises de début et de fin :

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large">

</Label>
```

Au sein de ces balises, ajoutez les balises de début et de fin qui se composent du nom de la classe et d’un nom de propriété séparés par un point :

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large">
    <Label.TextColor>

    </Label.TextColor>
</Label>
```

Définissez la valeur de la propriété en tant que contenu de ces nouvelles balises, comme suit :

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

Ces deux méthodes pour spécifier la `TextColor` propriété sont fonctionnellement équivalentes, mais n’utilisent pas les deux méthodes pour la même propriété, car cela permet de définir deux fois la propriété et peut être ambigu.

Avec cette nouvelle syntaxe, vous pouvez introduire une terminologie pratique :

- `Label` est un  *élément objet*. Il s’agit d’un Xamarin.Forms objet exprimé sous la forme d’un élément XML.
- `Text`,  `VerticalOptions` `FontAttributes` et  `FontSize` sont des  *attributs de propriété*. Il s’agit Xamarin.Forms de propriétés exprimées en tant qu’attributs XML.
- Dans cet extrait de code final, `TextColor` est devenu un  *élément de propriété*. Il s’agit d’une Xamarin.Forms propriété, mais elle est désormais un élément XML.

La définition des éléments de propriété peut sembler être une violation de la syntaxe XML, mais ce n’est pas le fait. Le point n’a aucune signification particulière dans XML. À un décodeur XML, `Label.TextColor` est simplement un élément enfant normal.

En XAML, toutefois, cette syntaxe est très spéciale. L’une des règles pour les éléments de propriété est que rien d’autre ne peut apparaître dans la `Label.TextColor` balise. La valeur de la propriété est toujours définie en tant que contenu entre les balises de début et de fin de l’élément de propriété.

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

Vous pouvez utiliser la syntaxe d’élément de propriété pour toutes les propriétés :

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

Dans un premier temps, la syntaxe des éléments de propriété peut sembler un remplacement à long terme inutile pour quelque chose de relativement simple, et dans ces exemples, c’est certainement le cas.

Toutefois, la syntaxe des éléments de propriété devient essentielle lorsque la valeur d’une propriété est trop complexe pour être exprimée sous la forme d’une chaîne simple. Dans les balises d’éléments de propriété, vous pouvez instancier un autre objet et définir ses propriétés. Par exemple, vous pouvez définir explicitement une propriété telle qu' `VerticalOptions` une `LayoutOptions` valeur avec les paramètres de propriété :

```xaml
<Label>
    ...
    <Label.VerticalOptions>
        <LayoutOptions Alignment="Center" />
    </Label.VerticalOptions>
</Label>
```

Autre exemple : `Grid` a deux propriétés nommées `RowDefinitions` et `ColumnDefinitions` . Ces deux propriétés sont de type `RowDefinitionCollection` et `ColumnDefinitionCollection` , qui sont des collections `RowDefinition` d' `ColumnDefinition` objets et. Vous devez utiliser la syntaxe d’élément de propriété pour définir ces collections.

Voici le début du fichier XAML pour une `GridDemoPage` classe, en présentant les balises des éléments de propriété pour les `RowDefinitions` `ColumnDefinitions` collections et :

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

Notez la syntaxe abrégée permettant de définir des cellules de taille automatique, des cellules de largeur et de hauteur de pixel, ainsi que des paramètres en étoile.

## <a name="attached-properties"></a>Propriétés jointes

Vous venez de voir que le `Grid` requiert des éléments de propriété pour les `RowDefinitions` `ColumnDefinitions` collections et pour définir les lignes et les colonnes. Toutefois, il doit également y avoir un moyen pour le programmeur d’indiquer la ligne et la colonne où chaque enfant du `Grid` réside.

Dans la balise pour chaque enfant du `Grid` , vous spécifiez la ligne et la colonne de cet enfant à l’aide des attributs suivants :

- `Grid.Row`
- `Grid.Column`

Les valeurs par défaut de ces attributs sont 0. Vous pouvez également indiquer si un enfant s’étend sur plusieurs lignes ou colonnes avec les attributs suivants :

- `Grid.RowSpan`
- `Grid.ColumnSpan`

Ces deux attributs ont des valeurs par défaut de 1.

Voici le fichier GridDemoPage. Xaml complet :

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

Les `Grid.Row` `Grid.Column` paramètres et de 0 ne sont pas obligatoires, mais sont généralement inclus à des fins de clarté.

Voici à quoi elle ressemble :

[![Disposition de grille](essential-xaml-syntax-images/griddemo.png)](essential-xaml-syntax-images/griddemo-large.png#lightbox)

En cas de jugement uniquement à partir de la syntaxe, ces attributs,, `Grid.Row` `Grid.Column` `Grid.RowSpan` et `Grid.ColumnSpan` semblent être des champs ou des propriétés statiques de `Grid` , mais ils sont intéressants, mais ils `Grid` ne définissent rien, `Row` `Column` , `RowSpan` ou `ColumnSpan` .

Au lieu de cela, `Grid` définit quatre propriétés pouvant être liées nommées `RowProperty` ,, `ColumnProperty` `RowSpanProperty` et `ColumnSpanProperty` . Il s’agit de types spéciaux de propriétés pouvant être liées, appelées *propriétés jointes*. Ils sont définis par la `Grid` classe, mais définis sur les enfants de `Grid` .

Lorsque vous souhaitez utiliser ces propriétés jointes dans le code, la `Grid` classe fournit des méthodes statiques nommées `SetRow` , `GetColumn` , et ainsi de suite. En XAML, ces propriétés jointes sont définies en tant qu’attributs dans les enfants de l' `Grid` utilisation de noms de propriétés simples.

Les propriétés jointes sont toujours reconnaissables dans les fichiers XAML en tant qu’attributs contenant à la fois une classe et un nom de propriété séparés par un point. Ils sont appelés *propriétés jointes* , car ils sont définis par une classe (dans ce cas, `Grid` ), mais attachés à d’autres objets (dans ce cas, les enfants de `Grid` ). Pendant la disposition, le `Grid` peut interroger les valeurs de ces propriétés jointes pour savoir où placer chaque enfant.

La `AbsoluteLayout` classe définit deux propriétés jointes nommées `LayoutBounds` et `LayoutFlags` . Voici un modèle de damier réalisé à l’aide des fonctionnalités de positionnement et de dimensionnement proportionnels de `AbsoluteLayout` :

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

Et ici :

[![Disposition absolue](essential-xaml-syntax-images/absolutedemo-large.png)](essential-xaml-syntax-images/absolutedemo-large.png#lightbox)

Pour ce type de situation, vous pouvez vous faire une question de l’utilisation de XAML. Certainement, la répétition et la régularité du `LayoutBounds` rectangle suggèrent qu’il peut être mieux réalisé dans le code.

C’est certainement un problème légitime et il n’y a aucun problème avec l’équilibrage de l’utilisation du code et du balisage lors de la définition de vos interfaces utilisateur. Il est facile de définir certains visuels dans XAML, puis d’utiliser le constructeur du fichier code-behind pour ajouter d’autres visuels qui peuvent être mieux générés dans des boucles.

## <a name="content-properties"></a>Propriétés de contenu

Dans les exemples précédents, les `StackLayout` `Grid` objets, et `AbsoluteLayout` ont la valeur de la `Content` propriété de `ContentPage` , et les enfants de ces dispositions sont en fait des éléments de la `Children` collection. Pourtant `Content` , ces `Children` Propriétés et ne sont pas dans le fichier XAML.

Vous pouvez certainement inclure les `Content` `Children` Propriétés et en tant qu’éléments de propriété, comme dans l’exemple **XamlPlusCode** :

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

Les éléments définis dans Xamarin.Forms pour une utilisation en XAML sont autorisés à avoir une propriété marquée dans l' `ContentProperty` attribut sur la classe. Si vous recherchez la `ContentPage` classe dans la documentation en ligne Xamarin.Forms , vous verrez cet attribut :

```csharp
[Xamarin.Forms.ContentProperty("Content")]
public class ContentPage : TemplatedPage
```

Cela signifie que les `Content` balises d’éléments de propriété ne sont pas requises. Tout contenu XML qui apparaît entre les balises de début et de fin `ContentPage` est supposé être assigné à la `Content` propriété.

 `StackLayout`, `Grid` , `AbsoluteLayout` et sont `RelativeLayout` tous dérivés de `Layout<View>` , et si vous recherchez `Layout<T>` dans la  Xamarin.Forms documentation, vous verrez un autre `ContentProperty` attribut :

```csharp
[Xamarin.Forms.ContentProperty("Children")]
public abstract class Layout<T> : Layout ...
```

Qui autorise l’ajout automatique du contenu de la disposition à la `Children` collection sans `Children` balises d’éléments de propriété explicites.

D’autres classes ont également des `ContentProperty` définitions d’attribut. Par exemple, la propriété de contenu de `Label` est `Text` . Consultez la documentation de l’API pour les autres utilisateurs.

## <a name="platform-differences-with-onplatform"></a>Différences de plateforme avec OnPlatform

Dans les applications à page unique, il est courant de définir la `Padding` propriété sur la page pour éviter de remplacer la barre d’État iOS. Dans le code, vous pouvez utiliser la `Device.RuntimePlatform` propriété à cet effet :

```csharp
if (Device.RuntimePlatform == Device.iOS)
{
    Padding = new Thickness(0, 20, 0, 0);
}
```

Vous pouvez également effectuer une opération similaire en XAML à l’aide des [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1) [`On`](xref:Xamarin.Forms.On) classes et. Tout d’abord, incluez les éléments de propriété de la `Padding` propriété près du haut de la page :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>

    </ContentPage.Padding>
    ...
</ContentPage>
```

Au sein de ces balises, incluez une `OnPlatform` balise. `OnPlatform` est une classe générique. Vous devez spécifier l’argument de type générique, dans le cas présent, `Thickness` , qui est le type de `Padding` propriété. Heureusement, il existe un attribut XAML spécifiquement pour définir des arguments génériques appelés `x:TypeArguments` . Cela doit correspondre au type de la propriété que vous définissez :

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

`OnPlatform` a une propriété nommée `Platforms` qui est un `IList` d' `On` objets. Utilisez des balises d’éléments de propriété pour cette propriété :

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

Ajoutez maintenant des `On` éléments. Pour chacun d’entre eux `Platform` , définissez la propriété et la `Value` propriété sur le balisage de la `Thickness` propriété :

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

Ce balisage peut être simplifié. La propriété de contenu de `OnPlatform` est `Platforms` , de sorte que ces balises d’élément de propriété peuvent être supprimées :

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

La `Platform` propriété de `On` est de type `IList<string>` , ce qui signifie que vous pouvez inclure plusieurs plateformes si les valeurs sont les mêmes :

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

Comme Android et UWP sont définis sur la valeur par défaut de `Padding` , cette balise peut être supprimée :

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

Il s’agit de la méthode standard pour définir une propriété dépendante de la plateforme `Padding` en XAML. Si le `Value` paramètre ne peut pas être représenté par une chaîne unique, vous pouvez définir des éléments de propriété pour celui-ci :

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
> L' `OnPlatform` extension de balisage peut également être utilisée en XAML pour personnaliser l’apparence de l’interface utilisateur pour chaque plateforme. Il fournit les mêmes fonctionnalités que les `OnPlatform` `On` classes et, mais avec une représentation plus concise. Pour plus d’informations, consultez [OnPlatform Markup Extension](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform-markup-extension).

## <a name="summary"></a>Résumé

Avec les éléments de propriété et les propriétés jointes, une grande partie de la syntaxe XAML de base a été établie. Toutefois, vous devez parfois définir des propriétés pour des objets de manière indirecte, par exemple, à partir d’un dictionnaire de ressources. Cette approche est traitée dans la partie suivante, partie [3. Extensions de balisage XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md).

## <a name="related-links"></a>Liens associés

- [XamlSamples](/samples/xamarin/xamarin-forms-samples/xamlsamples)
- [Partie 1. Prise en main avec XAML](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [Partie 3. Extensions de balisage XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Partie 4. Principes fondamentaux de la liaison de données](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [Partie 5. De la liaison de données au MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)