---
title: Xamarin. Forms ContentView
description: Cet article explique comment utiliser la classe ContentView pour créer un contrôle personnalisé, tel que l’exemple CardView.
ms.prod: xamarin
ms.assetid: 638402E7-CA44-456B-863B-791F6B6B561D
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 08/14/2019
ms.openlocfilehash: 712aa43fb4959b766786c8fd0969ef2c2c8f00ef
ms.sourcegitcommit: 211fed94fb96127a3e158ae1ff5d7eb831a203d8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75955732"
---
# <a name="xamarinforms-contentview"></a>Xamarin. Forms ContentView

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-contentviewdemos/)

La classe Xamarin. Forms [`ContentView`](xref:Xamarin.Forms.ContentView) est un type de `Layout` qui contient un seul élément enfant et est généralement utilisé pour créer des contrôles personnalisés et réutilisables. La classe `ContentView` hérite de [`TemplatedView`](xref:Xamarin.Forms.TemplatedView). Cet article, ainsi que les exemples associés, expliquent comment créer un contrôle de `CardView` personnalisé basé sur la classe `ContentView`.

La capture d’écran suivante montre un contrôle `CardView` qui dérive de la classe `ContentView` :

[capture d’écran de l’exemple d’application ![CardView](contentview-images/cardview-list-cropped.png)](contentview-images/cardview-list.png#lightbox)

La classe `ContentView` définit une propriété unique :

* [`Content`](xref:Xamarin.Forms.ContentView.Content) est un objet `View`. Cette propriété est stockée par un objet [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) afin de pouvoir être la cible des liaisons de données.

Le `ContentView` hérite également d’une propriété de la classe `TemplatedView` :

* [`ControlTemplate`](xref:Xamarin.Forms.TemplatedView.ControlTemplate) est un `ControlTemplate` qui peut définir ou substituer l’apparence du contrôle.

Pour plus d’informations sur la propriété `ControlTemplate`, consultez [personnaliser l’apparence avec un ControlTemplate](#customize-appearance-with-a-controltemplate).

## <a name="create-a-custom-control"></a>Créer un contrôle personnalisé

La classe `ContentView` offre peu de fonctionnalités, mais elle peut être utilisée pour créer un contrôle personnalisé. L’exemple de projet définit un contrôle de `CardView`, un élément d’interface utilisateur qui affiche une image, un titre et une description dans une disposition de type carte.

Le processus de création d’un contrôle personnalisé consiste à :

1. Créez une nouvelle classe à l’aide du modèle `ContentView` dans Visual Studio 2019.
1. Définissez des propriétés ou des événements uniques dans le fichier code-behind pour le nouveau contrôle personnalisé.
1. Créez l’interface utilisateur pour le contrôle personnalisé.

> [!NOTE]
> Il est possible de créer un contrôle personnalisé dont la disposition est définie dans le code au lieu de XAML. Par souci de simplicité, l’exemple d’application définit uniquement une seule classe `CardView` avec une disposition XAML. Toutefois, l’exemple d’application contient une classe **CardViewCodePage** qui montre le processus de consommation du contrôle personnalisé dans le code.

### <a name="create-code-behind-properties"></a>Créer des propriétés code-behind

Le `CardView` contrôle personnalisé définit les propriétés suivantes :

* `CardTitle`: objet `string` qui représente le titre affiché sur la carte.
* `CardDescription`: objet `string` qui représente la description affichée sur la carte.
* `IconImageSource`: objet `ImageSource` qui représente l’image affichée sur la carte.
* `IconBackgroundColor`: objet `Color` qui représente la couleur d’arrière-plan de l’image affichée sur la carte.
* `BorderColor`: objet `Color` qui représente la couleur de la bordure de la carte, de la bordure de l’image et de la ligne de séparation.
* `CardColor`: objet `Color` qui représente la couleur d’arrière-plan de la carte.

> [!NOTE]
> La propriété `BorderColor` affecte plusieurs éléments dans le cadre de la démonstration. Cette propriété peut être divisée en trois propriétés si nécessaire.

Chaque propriété est stockée par une instance de `BindableProperty`. Le `BindableProperty` de sauvegarde permet de styliser chaque propriété et de la lier à l’aide du modèle MVVM.

L’exemple suivant montre comment créer un `BindableProperty`de sauvegarde :

```csharp
public static readonly BindableProperty CardTitleProperty = BindableProperty.Create(
    "CardTitle",        // the name of the bindable property
    typeof(string),     // the bindable property type
    typeof(CardView),   // the parent object type
    string.Empty);      // the default value for the property
```

La propriété personnalisée utilise les méthodes `GetValue` et `SetValue` pour récupérer et définir les valeurs de l’objet `BindableProperty` :

```csharp
public string CardTitle
{
    get => (string)GetValue(CardView.CardTitleProperty);
    set => SetValue(CardView.CardTitleProperty, value);
}
```

Pour plus d’informations sur les objets `BindableProperty`, consultez [propriétés pouvant être liées](~/xamarin-forms/xaml/bindable-properties.md).

### <a name="define-ui"></a>Définir l’interface utilisateur

L’interface utilisateur du contrôle personnalisé utilise un `ContentView` en tant qu’élément racine pour le contrôle `CardView`. L’exemple suivant montre l' `CardView` XAML :

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

L’élément `ContentView` affecte à la propriété `x:Name` la valeur **This**, qui peut être utilisée pour accéder à l’objet lié à l’instance `CardView`. Les éléments de la disposition définissent des liaisons sur leurs propriétés aux valeurs définies sur l’objet lié.

Pour plus d’informations sur la liaison de données, consultez la page [Liaison de données Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md).

> [!NOTE]
> La propriété `FallbackValue` fournit une valeur par défaut au cas où la liaison est `null`. Cela permet également au générateur d' [Aperçu XAML](~/xamarin-forms/xaml/xaml-previewer/index.md) dans Visual Studio d’afficher le contrôle de `CardView`.

## <a name="instantiate-a-custom-control"></a>Instancier un contrôle personnalisé

Une référence à l’espace de noms de contrôle personnalisé doit être ajoutée à une page qui instancie le contrôle personnalisé. L’exemple suivant montre une référence d’espace de noms appelée **contrôles** ajoutés à une instance `ContentPage` en XAML :

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:CardViewDemo.Controls" >
```

Une fois la référence ajoutée, le `CardView` peut être instancié en XAML, et ses propriétés définies :

```xaml
<controls:CardView BorderColor="DarkGray"
                   CardTitle="Slavko Vlasic"
                   CardDescription="Lorem ipsum dolor sit..."
                   IconBackgroundColor="SlateGray"
                   IconImageSource="user.png"/>
```

Une `CardView` peut également être instanciée dans le code :

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

Un contrôle personnalisé qui dérive de la classe `ContentView` peut définir l’apparence à l’aide de XAML, du code ou ne pas définir l’apparence du tout. Quelle que soit la façon dont l’apparence est définie, un objet `ControlTemplate` peut remplacer l’apparence par une disposition personnalisée.

La disposition du `CardView` peut occuper trop d’espace pour certains cas d’usage. Une `ControlTemplate` peut remplacer la disposition `CardView` pour fournir une vue plus compacte, adaptée à une liste condensée :

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

La liaison de données dans un `ControlTemplate` utilise l’extension de balisage `TemplateBinding` pour spécifier des liaisons. La propriété `ControlTemplate` peut ensuite être définie sur l’objet ControlTemplate défini, à l’aide de sa valeur `x:Key`. L’exemple suivant montre le jeu de propriétés `ControlTemplate` sur une instance `CardView` :

```xaml
<controls:CardView ControlTemplate="{StaticResource CardViewCompressed}"/>
```

Les captures d’écran suivantes montrent une instance de `CardView` standard et `CardView` dont `ControlTemplate` a été remplacé :

[capture d’écran ![CardView ControlTemplate](contentview-images/cardview-controltemplates-cropped.png)](contentview-images/cardview-controltemplates.png#lightbox)

Pour plus d’informations sur les modèles de contrôle, consultez [modèles de contrôle Xamarin. Forms](~/xamarin-forms/app-fundamentals/templates/control-template.md).

## <a name="related-links"></a>Liens connexes

* [Exemple d’application ContentView](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-contentviewdemos/)
* [Liaison de données Xamarin. Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md)
* [Propriétés pouvant être liées](~/xamarin-forms/xaml/bindable-properties.md).
* [Modèles de contrôle Xamarin. Forms](~/xamarin-forms/app-fundamentals/templates/control-template.md)
