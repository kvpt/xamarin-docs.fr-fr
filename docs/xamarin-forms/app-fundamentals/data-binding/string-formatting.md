---
title: Formatage de chaîne dans Xamarin.Forms
description: Cet article explique comment utiliser les liaisons de données Xamarin.Forms pour formater et afficher des objets sous forme de chaînes. Pour cela, vous devez définir le StringFormat de la liaison sur une chaîne de formatage .NET standard avec un espace réservé.
ms.prod: xamarin
ms.assetid: 978C85B7-CB58-4483-A131-21B381A865E0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: 8efd93204b848113e0ed95c8066a5506eb517ac6
ms.sourcegitcommit: 5fc171a45697f7c610d65f74d1f3cebbac445de6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/20/2018
ms.locfileid: "52170947"
---
# <a name="xamarinforms-string-formatting"></a>Formatage de chaîne dans Xamarin.Forms

Il est parfois pratique d’utiliser des liaisons de données pour afficher la représentation de chaîne d’un objet ou d’une valeur. Par exemple, vous souhaiterez peut-être utiliser un objet `Label` pour afficher la valeur actuelle d’un élément `Slider`. Dans cette liaison de données, l’élément `Slider` est la source, et la cible est la propriété `Text` de l’objet `Label`.

Lors de l’affichage de chaînes dans le code, l’outil le plus puissant est la méthode statique [`String.Format`](xref:System.String.Format(System.String,System.Object)). La chaîne de formatage inclut des codes de formatage spécifiques à différents types d’objets, et vous pouvez inclure un autre texte avec les valeurs en cours de formatage. Pour plus d’informations sur le formatage de chaîne, consultez [Mettre en forme des types dans .NET](/dotnet/standard/base-types/formatting-types/).

## <a name="the-stringformat-property"></a>Propriété StringFormat

Cette fonctionnalité est reportée dans les liaisons de données : vous définissez la propriété [`StringFormat`](xref:Xamarin.Forms.BindingBase.StringFormat) de `Binding` (ou la propriété [`StringFormat`](xref:Xamarin.Forms.Xaml.BindingExtension.StringFormat) de l’extension de balisage `Binding`) sur une chaîne de formatage standard avec un espace réservé :

```xaml
<Slider x:Name="slider" />
<Label Text="{Binding Source={x:Reference slider},
                      Path=Value,
                      StringFormat='The slider value is {0:F2}'}" />
```

Notez que la chaîne de formatage est délimitée par des guillemets simples (apostrophes) afin d’aider l’analyseur XAML à éviter de traiter les accolades comme une autre extension de balisage XAML. Sinon, cette chaîne sans le guillemet simple est la même chaîne que vous utiliseriez pour afficher une valeur à virgule flottante dans un appel à `String.Format`. Une spécification de formatage `F2` entraîne l’affichage de la valeur avec deux décimales.

La propriété `StringFormat` est pertinente uniquement lorsque la propriété cible est de type `string` et que le mode de liaison est `OneWay` ou `TwoWay`. Pour des liaisons bidirectionnelles, la propriété `StringFormat` est applicable uniquement à des valeurs transmises de la source vers la cible.

Comme vous le verrez dans l’article suivant sur le [chemin de liaison](binding-path.md), les liaisons de données peuvent devenir relativement complexes. Lors du débogage des liaisons de données, vous pouvez ajouter un `Label` dans le fichier XAML avec un `StringFormat` pour afficher des résultats intermédiaires. Même si vous l’utilisez uniquement pour afficher le type d’un objet, il peut être utile.

La page **String Formatting** (Formatage de chaîne) illustre plusieurs utilisations de la propriété `StringFormat` :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:sys="clr-namespace:System;assembly=mscorlib"
             x:Class="DataBindingDemos.StringFormattingPage"
             Title="String Formatting">

    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Label">
                <Setter Property="HorizontalTextAlignment" Value="Center" />
            </Style>

            <Style TargetType="BoxView">
                <Setter Property="Color" Value="Blue" />
                <Setter Property="HeightRequest" Value="2" />
                <Setter Property="Margin" Value="0, 5" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout Margin="10">
        <Slider x:Name="slider" />
        <Label Text="{Binding Source={x:Reference slider},
                              Path=Value,
                              StringFormat='The slider value is {0:F2}'}" />

        <BoxView />

        <TimePicker x:Name="timePicker" />
        <Label Text="{Binding Source={x:Reference timePicker},
                              Path=Time,
                              StringFormat='The TimeSpan is {0:c}'}" />

        <BoxView />

        <Entry x:Name="entry" />
        <Label Text="{Binding Source={x:Reference entry},
                              Path=Text,
                              StringFormat='The Entry text is &quot;{0}&quot;'}" />

        <BoxView />

        <StackLayout BindingContext="{x:Static sys:DateTime.Now}">
            <Label Text="{Binding}" />
            <Label Text="{Binding Path=Ticks,
                                  StringFormat='{0:N0} ticks since 1/1/1'}" />
            <Label Text="{Binding StringFormat='The {{0:MMMM}} specifier produces {0:MMMM}'}" />
            <Label Text="{Binding StringFormat='The long date is {0:D}'}" />
        </StackLayout>

        <BoxView />

        <StackLayout BindingContext="{x:Static sys:Math.PI}">
            <Label Text="{Binding}" />
            <Label Text="{Binding StringFormat='PI to 4 decimal points = {0:F4}'}" />
            <Label Text="{Binding StringFormat='PI in scientific notation = {0:E7}'}" />
        </StackLayout>
    </StackLayout>
</ContentPage>
```

Les liaisons sur les éléments `Slider` et `TimePicker` illustrent l’utilisation de spécifications de format propres aux types de données `double` et `TimeSpan`. Le `StringFormat` qui affiche le texte à partir de la vue `Entry` montre comment spécifier des guillemets doubles dans la chaîne de formatage à l’aide de l’entité HTML `&quot;`.

La section suivante du fichier XAML est un `StackLayout` avec un `BindingContext` défini sur une extension de balisage `x:Static` qui référence la propriété statique `DateTime.Now`. La première liaison n’a pas de propriétés :

```xaml
<Label Text="{Binding}" />
```

Elle affiche simplement la valeur `DateTime` du `BindingContext` avec le formatage par défaut. La deuxième liaison affiche la propriété `Ticks` du `DateTime`, tandis que les deux autres liaisons affichent le `DateTime` lui-même avec un formatage spécifique. Notez ce `StringFormat` :

```xaml
<Label Text="{Binding StringFormat='The {{0:MMMM}} specifier produces {0:MMMM}'}" />
```

Si vous avez besoin d’afficher des accolades gauches ou droites dans votre chaîne de formatage, utilisez-en simplement une paire.

La dernière section définit le `BindingContext` sur la valeur de `Math.PI` et l’affiche avec le formatage par défaut et deux types différents de formatage numérique.

Voici le programme en cours d’exécution :

[![String Formatting](string-formatting-images/stringformatting-small.png "String Formatting")](string-formatting-images/stringformatting-large.png#lightbox "String Formatting")

## <a name="viewmodels-and-string-formatting"></a>Objets ViewModel et formatage de chaîne

Lorsque vous utilisez `Label` et `StringFormat` pour afficher la valeur d’une vue qui est également la cible d’un ViewModel, vous pouvez définir la liaison de la vue à l’objet `Label` ou du ViewModel à l’objet `Label`. En général, la deuxième approche est préférable car elle vérifie que les liaisons fonctionnent entre la vue et le ViewModel.

Cette approche est illustrée dans l’exemple **Better Color Selector** (Meilleur sélecteur de couleur) qui utilise le même ViewModel que le programme **Simple Color Selector** (Sélecteur de couleur simple) illustré dans l’article [**Mode de liaison**](binding-mode.md) :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.BetterColorSelectorPage"
             Title="Better Color Selector">

    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Slider">
                <Setter Property="VerticalOptions" Value="CenterAndExpand" />
            </Style>

            <Style TargetType="Label">
                <Setter Property="HorizontalTextAlignment" Value="Center" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout>
        <StackLayout.BindingContext>
            <local:HslColorViewModel Color="Sienna" />
        </StackLayout.BindingContext>

        <BoxView Color="{Binding Color}"
                 VerticalOptions="FillAndExpand" />

        <StackLayout Margin="10, 0">
            <Label Text="{Binding Name}" />

            <Slider Value="{Binding Hue}" />
            <Label Text="{Binding Hue, StringFormat='Hue = {0:F2}'}" />

            <Slider Value="{Binding Saturation}" />
            <Label Text="{Binding Saturation, StringFormat='Saturation = {0:F2}'}" />

            <Slider Value="{Binding Luminosity}" />
            <Label Text="{Binding Luminosity, StringFormat='Luminosity = {0:F2}'}" />
        </StackLayout>
    </StackLayout>
</ContentPage>    
```

Il existe désormais trois paires d’éléments `Slider` et `Label` qui sont liés à la même propriété source de l’objet `HslColorViewModel`. La seule différence est que l’objet `Label` a une propriété `StringFormat` pour afficher chaque valeur `Slider`.

[![Better Color Selector](string-formatting-images/bettercolorselector-small.png "Better Color Selector")](string-formatting-images/bettercolorselector-large.png#lightbox "Better Color Selector")

Vous vous demandez peut-être comment afficher les valeurs RVB (rouge, vert, bleu) au format hexadécimal à deux chiffres traditionnel. Ces valeurs entières ne sont pas directement disponibles à partir de la structure `Color`. Une solution consisterait à calculer les valeurs entières des composantes de couleur dans le ViewModel et à les exposer en tant que propriétés. Vous pourriez alors les formater à l’aide de la spécification de formatage `X2`.

Une autre approche est plus générale : vous pouvez écrire un *convertisseur de valeurs de liaison* comme indiqué dans le prochain article, [**Convertisseurs de valeurs de liaison**](converters.md).

Le prochain article, toutefois, explore le [**chemin de liaison**](binding-path.md) plus en détail et montre comment vous pouvez l’utiliser pour référencer des sous-propriétés et des éléments dans des collections.


## <a name="related-links"></a>Liens associés

- [Démos des liaisons de données (exemple)](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
- [Chapitre sur les liaisons de données dans la documentation de Xamarin.Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
