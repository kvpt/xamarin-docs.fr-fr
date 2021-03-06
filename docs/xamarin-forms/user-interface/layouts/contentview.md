---
title: Xamarin.Forms ContentView
description: Cet article explique comment utiliser la classe ContentView pour créer un contrôle personnalisé, tel que l’exemple CardView.
ms.prod: xamarin
ms.assetid: 638402E7-CA44-456B-863B-791F6B6B561D
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 08/14/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: a11178b9034561e9f1fdf1bbf17cf28cfe9d967b
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93375081"
---
# <a name="no-locxamarinforms-contentview"></a>Xamarin.Forms ContentView

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](/samples/xamarin/xamarin-forms-samples/userinterface-contentviewdemos/)

La Xamarin.Forms [`ContentView`](xref:Xamarin.Forms.ContentView) classe est un type de `Layout` qui contient un seul élément enfant et est généralement utilisé pour créer des contrôles personnalisés et réutilisables. La `ContentView` classe hérite de [`TemplatedView`](xref:Xamarin.Forms.TemplatedView) . Cet article, ainsi que l’exemple associé, expliquent comment créer un `CardView` contrôle personnalisé basé sur la `ContentView` classe.

La capture d’écran suivante montre un `CardView` contrôle qui dérive de la `ContentView` classe :

[![Capture d’écran de l’exemple d’application CardView](contentview-images/cardview-list-cropped.png)](contentview-images/cardview-list.png#lightbox)

La `ContentView` classe définit une propriété unique :

* [`Content`](xref:Xamarin.Forms.ContentView.Content) est un `View` objet. Cette propriété est stockée par un [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objet afin de pouvoir être la cible des liaisons de données.

`ContentView`Hérite également d’une propriété de la `TemplatedView` classe :

* [`ControlTemplate`](xref:Xamarin.Forms.TemplatedView.ControlTemplate) est un `ControlTemplate` qui peut définir ou substituer l’apparence du contrôle.

Pour plus d’informations sur la `ControlTemplate` propriété, consultez [personnaliser l’apparence avec un ControlTemplate](#customize-appearance-with-a-controltemplate).

## <a name="create-a-custom-control"></a>Créer un contrôle personnalisé

La `ContentView` classe offre peu de fonctionnalités, mais elle peut être utilisée pour créer un contrôle personnalisé. L’exemple de projet définit un `CardView` contrôle (un élément d’interface utilisateur qui affiche une image, un titre et une description dans une disposition de type carte).

Le processus de création d’un contrôle personnalisé consiste à :

1. Créez une nouvelle classe à l’aide du `ContentView` modèle dans Visual Studio 2019.
1. Définissez des propriétés ou des événements uniques dans le fichier code-behind pour le nouveau contrôle personnalisé.
1. Créez l’interface utilisateur pour le contrôle personnalisé.

> [!NOTE]
> Il est possible de créer un contrôle personnalisé dont la disposition est définie dans le code au lieu de XAML. Par souci de simplicité, l’exemple d’application définit uniquement une seule `CardView` classe avec une disposition XAML. Toutefois, l’exemple d’application contient une classe **CardViewCodePage** qui montre le processus de consommation du contrôle personnalisé dans le code.

### <a name="create-code-behind-properties"></a>Créer des propriétés code-behind

Le `CardView` contrôle personnalisé définit les propriétés suivantes :

* `CardTitle`: `string` objet qui représente le titre affiché sur la carte.
* `CardDescription`: `string` objet qui représente la description affichée sur la carte.
* `IconImageSource`: `ImageSource` objet qui représente l’image affichée sur la carte.
* `IconBackgroundColor`: `Color` objet qui représente la couleur d’arrière-plan de l’image affichée sur la carte.
* `BorderColor`: `Color` objet qui représente la couleur de la bordure de la carte, de la bordure de l’image et de la ligne de séparation.
* `CardColor`: `Color` objet qui représente la couleur d’arrière-plan de la carte.

> [!NOTE]
> La `BorderColor` propriété affecte plusieurs éléments dans le cadre de la démonstration. Cette propriété peut être divisée en trois propriétés si nécessaire.

Chaque propriété est stockée par une `BindableProperty` instance de. Le stockage `BindableProperty` permet à chaque propriété d’être stylisée, et liée, à l’aide du modèle MVVM.

L’exemple suivant montre comment créer une sauvegarde `BindableProperty` :

```csharp
public static readonly BindableProperty CardTitleProperty = BindableProperty.Create(
    "CardTitle",        // the name of the bindable property
    typeof(string),     // the bindable property type
    typeof(CardView),   // the parent object type
    string.Empty);      // the default value for the property
```

La propriété personnalisée utilise les `GetValue` `SetValue` méthodes et pour récupérer et définir les `BindableProperty` valeurs de l’objet :

```csharp
public string CardTitle
{
    get => (string)GetValue(CardView.CardTitleProperty);
    set => SetValue(CardView.CardTitleProperty, value);
}
```

Pour plus d’informations sur les `BindableProperty` objets, consultez [propriétés pouvant être liées](~/xamarin-forms/xaml/bindable-properties.md).

### <a name="define-ui"></a>Définir l’interface utilisateur

L’interface utilisateur du contrôle personnalisé utilise `ContentView` comme élément racine pour le `CardView` contrôle. L’exemple suivant montre le `CardView` code XAML :

```XAML
<ContentView ...
             x:Name="this"
             x:Class="CardViewDemo.Controls.CardView">
    <Frame BindingContext="{x:Reference this}"
            BackgroundColor="{Binding CardColor}"
            BorderColor="{Binding BorderColor}"
            ...>
        <Grid>
            ...
            <Frame BorderColor="{Binding BorderColor, FallbackValue='Black'}"
                   BackgroundColor="{Binding IconBackgroundColor, FallbackValue='Grey'}"
                   ...>
                <Image Source="{Binding IconImageSource}"
                       .. />
            </Frame>
            <Label Text="{Binding CardTitle, FallbackValue='Card Title'}"
                   ... />
            <BoxView BackgroundColor="{Binding BorderColor, FallbackValue='Black'}"
                     ... />
            <Label Text="{Binding CardDescription, FallbackValue='Card description text.'}"
                   ... />
        </Grid>
    </Frame>
</ContentView>
```

L' `ContentView` élément affecte la valeur `x:Name` à **this** la propriété, qui peut être utilisée pour accéder à l’objet lié à l' `CardView` instance. Les éléments de la disposition définissent des liaisons sur leurs propriétés aux valeurs définies sur l’objet lié.

Pour plus d’informations sur la liaison de données, consultez [ Xamarin.Forms liaison de données](~/xamarin-forms/app-fundamentals/data-binding/index.md).

> [!NOTE]
> La `FallbackValue` propriété fournit une valeur par défaut si la liaison est `null` . Cela permet également au générateur d' [Aperçu XAML](~/xamarin-forms/xaml/xaml-previewer/index.md) dans Visual Studio de restituer le `CardView` contrôle.

## <a name="instantiate-a-custom-control"></a>Instancier un contrôle personnalisé

Une référence à l’espace de noms de contrôle personnalisé doit être ajoutée à une page qui instancie le contrôle personnalisé. L’exemple suivant montre une référence d’espace de noms appelée **contrôles** ajoutés à une `ContentPage` instance en XAML :

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:CardViewDemo.Controls" >
```

Une fois la référence ajoutée, le `CardView` peut être instancié en XAML et ses propriétés définies :

```xaml
<controls:CardView BorderColor="DarkGray"
                   CardTitle="Slavko Vlasic"
                   CardDescription="Lorem ipsum dolor sit..."
                   IconBackgroundColor="SlateGray"
                   IconImageSource="user.png"/>
```

Un `CardView` peut également être instancié dans le code :

```csharp
CardView card = new CardView
{
    BorderColor = Color.DarkGray,
    CardTitle = "Slavko Vlasic",
    CardDescription = "Lorem ipsum dolor sit...",
    IconBackgroundColor = Color.SlateGray,
    IconImageSource = ImageSource.FromFile("user.png")
};
```

## <a name="customize-appearance-with-a-controltemplate"></a>Personnaliser l’apparence avec un ControlTemplate

Un contrôle personnalisé qui dérive de la `ContentView` classe peut définir l’apparence à l’aide de XAML, du code ou ne pas définir l’apparence du tout. Quelle que soit la façon dont l’apparence est définie, un `ControlTemplate` objet peut remplacer l’apparence par une disposition personnalisée.

La `CardView` disposition peut occuper trop d’espace pour certains cas d’usage. Un `ControlTemplate` peut remplacer la `CardView` disposition pour fournir une vue plus compacte, adaptée à une liste condensée :

```xaml
<ContentPage.Resources>
    <ResourceDictionary>
        <ControlTemplate x:Key="CardViewCompressed">
            <Grid>
                <Grid.RowDefinitions>
                    <RowDefinition Height="100" />
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="100" />
                    <ColumnDefinition Width="100*" />
                </Grid.ColumnDefinitions>
                <Image Grid.Row="0"
                       Grid.Column="0"
                       Source="{TemplateBinding IconImageSource}"
                       BackgroundColor="{TemplateBinding IconBackgroundColor}"
                       WidthRequest="100"
                       HeightRequest="100"
                       Aspect="AspectFill"
                       HorizontalOptions="Center"
                       VerticalOptions="Center"/>
                <StackLayout Grid.Row="0"
                             Grid.Column="1">
                    <Label Text="{TemplateBinding CardTitle}"
                           FontAttributes="Bold" />
                    <Label Text="{TemplateBinding CardDescription}" />
                </StackLayout>
            </Grid>
        </ControlTemplate>
    </ResourceDictionary>
</ContentPage.Resources>
```

La liaison de données dans un `ControlTemplate` utilise l' `TemplateBinding` extension de balisage pour spécifier des liaisons. La `ControlTemplate` propriété peut ensuite être définie sur l’objet ControlTemplate défini, à l’aide de sa `x:Key` valeur. L’exemple suivant montre le `ControlTemplate` jeu de propriétés sur une `CardView` instance :

```xaml
<controls:CardView ControlTemplate="{StaticResource CardViewCompressed}"/>
```

Les captures d’écran suivantes montrent une `CardView` instance standard et `CardView` dont `ControlTemplate` a été remplacé :

[![Capture d’écran de ControlTemplate ControlTemplate](contentview-images/cardview-controltemplates-cropped.png)](contentview-images/cardview-controltemplates.png#lightbox)

Pour plus d’informations sur les modèles de contrôle, consultez [ Xamarin.Forms modèles de contrôle](~/xamarin-forms/app-fundamentals/templates/control-template.md).

## <a name="related-links"></a>Liens connexes

* [Exemple d’application ContentView](/samples/xamarin/xamarin-forms-samples/userinterface-contentviewdemos/)
* [Xamarin.Forms Liaison de données](~/xamarin-forms/app-fundamentals/data-binding/index.md)
* [Propriétés pouvant être liées](~/xamarin-forms/xaml/bindable-properties.md).
* [Xamarin.Forms Modèles de contrôle](~/xamarin-forms/app-fundamentals/templates/control-template.md)