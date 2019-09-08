---
title: Liaisons de base Xamarin.Forms
description: Cet article explique comment utiliser une liaison de données Xamarin.Forms, qui lie une paire de propriétés entre deux objets, dont au moins un est généralement un objet d’interface utilisateur. Ces deux objets sont appelés la cible et la source.
ms.prod: xamarin
ms.assetid: 96553DF7-12EA-4FB2-AE85-3D1D59382B40
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/22/2019
ms.custom: video
ms.openlocfilehash: d981307ae96f75c67442d3898255c8bed62f1cf7
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70771717"
---
# <a name="xamarinforms-basic-bindings"></a>Liaisons de base Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)

Une liaison de données Xamarin.Forms lie une paire de propriétés entre deux objets, dont au moins un est généralement un objet d’interface utilisateur. Ces deux objets sont appelés la *cible* et la *source* :

- La *cible* est l’objet (et la propriété) sur lequel la liaison de données est définie.
- La *source* est l’objet (et la propriété) référencé par la liaison de données.

Cette distinction peut parfois porter à confusion : Dans le cas le plus simple, les données circulent de la source vers la cible, ce qui signifie que la valeur de la propriété cible est définie à partir de la valeur de la propriété source. Toutefois, dans certains cas, les données peuvent également circuler de la cible vers la source ou dans les deux sens. Pour éviter toute confusion, n’oubliez pas que la cible est toujours l’objet sur lequel la liaison de données est définie, même si elle fournit des données au lieu d’en recevoir.

## <a name="bindings-with-a-binding-context"></a>Liaisons avec un contexte de liaison

Les liaisons de données sont généralement spécifiées entièrement en XAML, mais il est intéressant de voir des liaisons de données dans le code. La page **Basic Code Binding** (Liaison de code élémentaire) contient un fichier XAML avec un `Label` et un `Slider` :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DataBindingDemos.BasicCodeBindingPage"
             Title="Basic Code Binding">
    <StackLayout Padding="10, 0">
        <Label x:Name="label"
               Text="TEXT"
               FontSize="48"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <Slider x:Name="slider"
                Maximum="360"
                VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

L’élément `Slider` est défini pour une plage de 0 à 360. L’objectif de ce programme est de faire pivoter l’objet `Label` en manipulant l’élément `Slider`.

Sans liaisons de données, vous devez définir l’événement `ValueChanged` de l’élément `Slider` sur un gestionnaire d’événements qui accède à la propriété `Value` de l’élément `Slider` et définit cette valeur sur la propriété `Rotation` de l’objet `Label`. La liaison de données automatise ce travail ; le gestionnaire d’événements et le code qu’il contient ne sont plus nécessaires.

Vous pouvez définir une liaison sur une instance de n’importe quelle classe qui dérive de [`BindableObject`](xref:Xamarin.Forms.BindableObject) et qui inclut les dérivés `Element`, `VisualElement`, `View` et `View`.  La liaison est toujours définie sur l’objet cible. La liaison fait référence à l’objet source. Pour définir la liaison de données, utilisez les deux membres suivants de la classe cible :

- La propriété [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) spécifie l’objet source.
- La méthode [`SetBinding`](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase)) spécifie la propriété cible et la propriété source.

Dans cet exemple, l’objet `Label` est la cible de la liaison et l’élément `Slider` en est la source. Des modifications dans l’élément `Slider` source affectent la rotation de l’objet `Label` cible. Les données circulent de la source vers la cible.

La méthode `SetBinding` définie par `BindableObject` a un argument de type [`BindingBase`](xref:Xamarin.Forms.BindingBase) à partir duquel la classe [`Binding`](xref:Xamarin.Forms.Binding) dérive, mais d’autres méthodes `SetBinding` sont définies par la classe [`BindableObjectExtensions`](xref:Xamarin.Forms.BindableObjectExtensions). Le fichier code-behind dans l’exemple **Basic Code Binding** utilise une méthode d’extension [`SetBinding`](xref:Xamarin.Forms.BindableObjectExtensions.SetBinding*) plus simple à partir de cette classe.

```csharp
public partial class BasicCodeBindingPage : ContentPage
{
    public BasicCodeBindingPage()
    {
        InitializeComponent();

        label.BindingContext = slider;
        label.SetBinding(Label.RotationProperty, "Value");
    }
}
```

L’objet `Label` est la cible de la liaison et constitue donc l’objet sur lequel cette propriété est définie et sur lequel la méthode est appelée. La propriété `BindingContext` indique la source de la liaison, à savoir l’élément `Slider`.

La méthode `SetBinding` est appelée sur la cible de la liaison, mais spécifie la propriété cible et la propriété source. La propriété cible est spécifiée en tant qu’objet `BindableProperty` : `Label.RotationProperty`. La propriété source est spécifiée en tant que chaîne et indique la propriété `Value` de l’élément `Slider`.

La méthode `SetBinding` révèle l’une des règles les plus importantes des liaisons de données :

*La propriété cible doit reposer sur une propriété pouvant être liée.*

Cette règle implique que l’objet cible doit être une instance d’une classe qui dérive de `BindableObject`. Consultez l’article [**Propriétés pouvant être liées**](~/xamarin-forms/xaml/bindable-properties.md) pour obtenir une vue d’ensemble des objets et des propriétés pouvant être liés.

Il n’y a pas de règle de ce type pour la propriété source, qui est spécifiée en tant que chaîne. En interne, la réflexion est utilisée pour accéder à la propriété réelle. Dans ce cas particulier, toutefois, la propriété `Value` repose également sur une propriété pouvant être liée.

Le code peut être quelque peu simplifié : La propriété pouvant être liée `RotationProperty` est définie par `VisualElement` et héritée par `Label` et `ContentPage`. Par conséquent, le nom de la classe n’est pas requis dans l’appel `SetBinding` :

```csharp
label.SetBinding(RotationProperty, "Value");
```

Toutefois, inclure le nom de classe permet de se rappeler de l’objet cible.

Lorsque vous manipulez l’objet `Slider`, l’objet `Label` pivote en conséquence :

[![Basic Code Binding](basic-bindings-images/basiccodebinding-small.png "Basic Code Binding")](basic-bindings-images/basiccodebinding-large.png#lightbox "Basic Code Binding")

La page **Basic Xaml Binding** (Liaison Xaml de base) est identique à la page **Basic Code Binding**, si ce n’est qu’elle définit la liaison de données entière en XAML :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DataBindingDemos.BasicXamlBindingPage"
             Title="Basic XAML Binding">
    <StackLayout Padding="10, 0">
        <Label Text="TEXT"
               FontSize="80"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand"
               BindingContext="{x:Reference Name=slider}"
               Rotation="{Binding Path=Value}" />

        <Slider x:Name="slider"
                Maximum="360"
                VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

Comme dans le code, la liaison de données est définie sur l’objet cible, qui est l’objet `Label`. Deux extensions de balisage XAML sont impliquées. Elles sont instantanément reconnaissables à leurs accolades de délimitation :

- L’extension de balisage `x:Reference` est nécessaire pour référencer l’objet source, qui est l’objet `Slider` nommé `slider`.
- L’extension de balisage `Binding` lie la propriété `Rotation` de l’objet `Label` à la propriété `Value` de l’élément `Slider`.

Pour plus d’informations sur les extensions de balisage XAML, consultez l’article [Extensions de balisage XAML](~/xamarin-forms/xaml/markup-extensions/index.md). L’extension de balisage `x:Reference` est prise en charge par la classe [`ReferenceExtension`](xref:Xamarin.Forms.Xaml.ReferenceExtension) ; `Binding` est prise en charge par la classe [`BindingExtension`](xref:Xamarin.Forms.Xaml.BindingExtension). Comme l’indiquent les préfixes d’espace de noms XML, `x:Reference` fait partie de la spécification XAML 2009, tandis que `Binding` fait partie de Xamarin.Forms. Notez qu’aucun guillemet n’apparaît entre les accolades.

Il est facile d’oublier l’extension de balisage `x:Reference` lors de la définition de `BindingContext`. Il est courant de définir par inadvertance la propriété directement sur le nom de la source de la liaison, comme suit :

```xaml
BindingContext="slider"
```

Mais ce n'est pas correct. Ce balisage définit la propriété `BindingContext` sur un objet `string` dont les caractères forment le mot « slider ».

Notez que la propriété source est spécifiée avec la propriété [`Path`](xref:Xamarin.Forms.Xaml.BindingExtension.Path) de `BindingExtension`, qui correspond à la propriété [`Path`](xref:Xamarin.Forms.Binding.Path) de la classe [`Binding`](xref:Xamarin.Forms.Binding).

Le balisage affiché dans la page **Basic XAML Binding** peut être simplifié : Les extensions de balisage XAML telles que `x:Reference` et `Binding` peuvent avoir des attributs de *propriété de contenu* définis, ce qui signifie pour des extensions de balisage XAML que le nom de propriété n’a pas besoin d’apparaître. La propriété `Name` est la propriété de contenu de `x:Reference` et la propriété `Path` est la propriété de contenu de `Binding`, ce qui signifie que vous pouvez les éliminer des expressions :

```xaml
<Label Text="TEXT"
       FontSize="80"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand"
       BindingContext="{x:Reference slider}"
       Rotation="{Binding Value}" />
```

## <a name="bindings-without-a-binding-context"></a>Liaisons sans contexte de liaison

La propriété `BindingContext` est un composant important des liaisons de données, mais elle n’est pas toujours nécessaire. L’objet source peut être spécifié à la place dans l’appel `SetBinding` ou dans l’extension de balisage `Binding`.

Cela est illustré dans l’exemple **Alternative Code Binding** (Liaison de code alternative). Le fichier XAML est similaire à l’exemple **Basic Code Binding**, si ce n’est que l’élément `Slider` est défini pour contrôler la propriété `Scale` de l’objet `Label`. Pour cette raison, l’élément `Slider` est défini pour une plage allant de &ndash;2 à 2 :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DataBindingDemos.AlternativeCodeBindingPage"
             Title="Alternative Code Binding">
    <StackLayout Padding="10, 0">
        <Label x:Name="label"
               Text="TEXT"
               FontSize="40"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <Slider x:Name="slider"
                Minimum="-2"
                Maximum="2"
                VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

Le fichier code-behind définit la liaison avec la méthode [`SetBinding`](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase)) définie par `BindableObject`. L’argument est un [constructeur](xref:Xamarin.Forms.Binding.%23ctor(System.String,Xamarin.Forms.BindingMode,Xamarin.Forms.IValueConverter,System.Object,System.String,System.Object)) pour la classe [`Binding`](xref:Xamarin.Forms.Binding) :

```csharp
public partial class AlternativeCodeBindingPage : ContentPage
{
    public AlternativeCodeBindingPage()
    {
        InitializeComponent();

        label.SetBinding(Label.ScaleProperty, new Binding("Value", source: slider));
    }
}
```

Le constructeur `Binding` possède 6 paramètres, si bien que le paramètre `source` est spécifié avec un argument nommé. L’argument est l’élément `slider`.

L’exécution de ce programme peut être quelque peu surprenante :

[![Alternative Code Binding](basic-bindings-images/alternativecodebinding-small.png "Alternative Code Binding")](basic-bindings-images/alternativecodebinding-large.png#lightbox "Alternative Code Binding")

L’écran iOS de gauche montre à quoi ressemble l’écran lorsque la page apparaît initialement. Où est l’objet `Label` ?

Le problème est que l’élément `Slider` a une valeur initiale de 0. Par conséquent, la propriété `Scale` de l’objet `Label` est également définie sur 0, ce qui remplace sa valeur par défaut 1. Il en résulte que l’objet `Label` est initialement invisible. Comme les captures d’écran d’Android et de la plateforme Windows universelle l’illustrent, vous pouvez manipuler l’élément `Slider` pour faire réapparaître l’objet `Label`, mais sa disparition initiale est déconcertante.

Vous découvrirez dans le [prochain article](binding-mode.md) comment éviter ce problème en initialisant l’élément `Slider` à partir de la valeur par défaut de la propriété `Scale`.

> [!NOTE]
> La classe [`VisualElement`](xref:Xamarin.Forms.VisualElement) définit également les propriétés [`ScaleX`](xref:Xamarin.Forms.VisualElement.ScaleX) et [`ScaleY`](xref:Xamarin.Forms.VisualElement.ScaleY), qui peuvent redimensionner l’élément `VisualElement` dans les directions horizontale et verticale.

La page **Alternative XAML Binding** (Liaison XAML alternative) illustre une liaison équivalente entièrement en XAML :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DataBindingDemos.AlternativeXamlBindingPage"
             Title="Alternative XAML Binding">
    <StackLayout Padding="10, 0">
        <Label Text="TEXT"
               FontSize="40"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand"
               Scale="{Binding Source={x:Reference slider},
                               Path=Value}" />

        <Slider x:Name="slider"
                Minimum="-2"
                Maximum="2"
                VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

Maintenant, l’extension de balisage `Binding` a deux propriétés définies, `Source` et `Path`, séparées par une virgule. Elles peuvent apparaître sur la même ligne, si vous préférez :

```xaml
Scale="{Binding Source={x:Reference slider}, Path=Value}" />
```

La propriété `Source` est définie sur une extension de balisage `x:Reference` qui possède autrement la même syntaxe que la définition de `BindingContext`. Notez qu’aucun guillemet ne figure entre les accolades et que les deux propriétés doivent être séparées par une virgule.

La propriété de contenu de l’extension de balisage `Binding` est `Path`, mais la partie `Path=` de l’extension de balisage peut être éliminée seulement s’il s’agit de la première propriété dans l’expression. Pour éliminer la partie `Path=`, vous devez échanger les deux propriétés :

```xaml
Scale="{Binding Value, Source={x:Reference slider}}" />
```

Bien que les extensions de balisage XAML soient généralement délimitées par des accolades, elles peuvent également être exprimées en tant qu’éléments objets :

```xaml
<Label Text="TEXT"
       FontSize="40"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand">
    <Label.Scale>
        <Binding Source="{x:Reference slider}"
                 Path="Value" />
    </Label.Scale>
</Label>
```

Maintenant, les propriétés `Source` et `Path` sont des attributs XAML réguliers : Les valeurs apparaissent entre guillemets et les attributs ne sont pas séparés par une virgule. L’extension de balisage `x:Reference` peut également devenir un élément objet :

```xaml
<Label Text="TEXT"
       FontSize="40"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand">
    <Label.Scale>
        <Binding Path="Value">
            <Binding.Source>
                <x:Reference Name="slider" />
            </Binding.Source>
        </Binding>
    </Label.Scale>
</Label>
```

Cette syntaxe n’est pas courante, mais elle est parfois nécessaire lorsque des objets complexes sont impliqués.

Les exemples présentés jusqu'à présent définissent la propriété `BindingContext` et la propriété `Source` de `Binding` sur une extension de balisage `x:Reference` pour référencer une autre vue dans la page. Ces deux propriétés sont de type `Object` et elles peuvent être définies sur n’importe quel objet incluant des propriétés qui conviennent pour des sources de liaison.

Dans les articles à venir, vous découvrirez que vous pouvez définir la propriété `BindingContext` ou `Source` sur une extension de balisage `x:Static` pour référencer la valeur d’un champ ou d’une propriété statique, ou une extension de balisage `StaticResource` pour référencer un objet stocké dans un dictionnaire de ressources, ou directement sur un objet, qui est généralement (mais pas toujours) une instance d’un ViewModel.

La propriété `BindingContext` peut également être définie sur un objet `Binding`, afin que les propriétés `Source` et `Path` de `Binding` définissent le contexte de liaison.

## <a name="binding-context-inheritance"></a>Héritage du contexte de liaison

Dans cet article, vous avez vu que vous pouvez spécifier l’objet source en utilisant la propriété `BindingContext` ou la propriété `Source` de l’objet `Binding`. Si les deux sont définis, la propriété `Source` de `Binding` est prioritaire sur `BindingContext`.

La propriété `BindingContext` a une caractéristique extrêmement importante :

*Le paramètre de la propriété `BindingContext` est hérité via l’arborescence d'éléments visuels.*

Comme vous le verrez, cela peut être très pratique pour simplifier les expressions de liaison et dans certains cas &mdash; en particulier dans les scénarios MVVM (modèle-vue-vue modèle) &mdash; c’est essentiel.

L’exemple **Binding Context Inheritance** (Héritage du contexte de liaison) est une simple illustration de l’héritage du contexte de liaison :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DataBindingDemos.BindingContextInheritancePage"
             Title="BindingContext Inheritance">
    <StackLayout Padding="10">

        <StackLayout VerticalOptions="FillAndExpand"
                     BindingContext="{x:Reference slider}">

            <Label Text="TEXT"
                   FontSize="80"
                   HorizontalOptions="Center"
                   VerticalOptions="EndAndExpand"
                   Rotation="{Binding Value}" />

            <BoxView Color="#800000FF"
                     WidthRequest="180"
                     HeightRequest="40"
                     HorizontalOptions="Center"
                     VerticalOptions="StartAndExpand"
                     Rotation="{Binding Value}" />
        </StackLayout>

        <Slider x:Name="slider"
                Maximum="360" />

    </StackLayout>
</ContentPage>
```

La propriété `BindingContext` de `StackLayout` est définie sur l’élément `slider`. Ce contexte de liaison est hérité par les deux objets `Label` et `BoxView`, qui ont tous les deux leur propriété `Rotation` définie sur la propriété `Value` de l’élément `Slider` :

[![Binding Context Inheritance](basic-bindings-images/bindingcontextinheritance-small.png "Binding Context Inheritance")](basic-bindings-images/bindingcontextinheritance-large.png#lightbox "Binding Context Inheritance")

Dans le [prochain article](binding-mode.md), vous verrez comment le *mode de liaison* peut modifier le flux de données entre les objets cible et source.

## <a name="related-links"></a>Liens associés

- [Démos des liaisons de données (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)
- [Chapitre sur les liaisons de données dans la documentation de Xamarin.Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)

## <a name="related-video"></a>Vidéo associée

> [!Video https://channel9.msdn.com/Shows/XamarinShow/XamarinForms-101-Data-Binding/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
