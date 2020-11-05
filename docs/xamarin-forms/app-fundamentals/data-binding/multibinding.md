---
title: Liaisons multiples Xamarin. Forms
description: Cet article explique comment attacher une collection d’objets de liaison à une propriété de cible de liaison unique à l’aide de la classe MultiBinding.
ms.prod: xamarin
ms.assetid: E73AE622-664C-4A90-B5B2-BD47D0E7A1A7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/26/2020
ms.openlocfilehash: 79d27cf9d6ba01235962b43ee7f05c904c265c2f
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93370843"
---
# <a name="xamarinforms-multi-bindings"></a>Liaisons multiples Xamarin. Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](/samples/xamarin/xamarin-forms-samples/databindingdemos)

Les liaisons multiples offrent la possibilité d’attacher une collection d' [`Binding`](xref:Xamarin.Forms.Binding) objets à une propriété de cible de liaison unique. Ils sont créés avec la `MultiBinding` classe, qui évalue tous ses `Binding` objets et retourne une valeur unique par le biais d’une `IMultiValueConverter` instance fournie par votre application. En outre, `MultiBinding` réévalue tous ses `Binding` objets lorsqu’une des données liées est modifiée.

La `MultiBinding` classe définit les propriétés suivantes :

- `Bindings`, de type `IList<BindingBase>` , qui représente la collection d' [`Binding`](xref:Xamarin.Forms.Binding) objets dans l' `MultiBinding` instance.
- `Converter`, de type `IMultiValueConverter` , qui représente le convertisseur à utiliser pour convertir les valeurs sources vers ou à partir de la valeur cible.
- `ConverterParameter`, de type `object` , qui représente un paramètre facultatif à passer à `Converter` .

La `Bindings` propriété est la propriété de contenu de la `MultiBinding` classe et n’a donc pas besoin d’être explicitement définie à partir de XAML.

En outre, la `MultiBinding` classe hérite des propriétés suivantes de la `BindingBase` classe :

- `FallbackValue`, de type `object` , qui représente la valeur à utiliser lorsque la liaison multiple ne peut pas retourner de valeur.
- `Mode`, de type [`BindingMode`](xref:Xamarin.Forms.BindingMode) , qui indique la direction du workflow de la liaison multiple.
- `StringFormat`, de type `string` , qui spécifie comment mettre en forme le résultat de la liaison multiple s’il est affiché sous la forme d’une chaîne.
- `TargetNullValue`, de type `object` , qui représente la valeur utilisée dans le lorsque cible, la valeur de la source est `null` .

Un `MultiBinding` doit utiliser un `IMultiValueConverter` pour produire une valeur pour la cible de liaison, en fonction de la valeur des liaisons dans la `Bindings` collection. Par exemple, un [`Color`](xref:Xamarin.Forms.Color) peut être calculé à partir de valeurs de rouge, de bleu et de vert, qui peuvent être des valeurs d’objets de source de liaison identiques ou différents. Lorsqu’une valeur est déplacée de la cible vers les sources, la valeur de la propriété cible est convertie en un ensemble de valeurs qui sont renvoyées dans les liaisons.

> [!IMPORTANT]
> Les liaisons individuelles de la `Bindings` collection peuvent avoir leurs propres convertisseurs de valeur.

La valeur de la `Mode` propriété détermine les fonctionnalités de `MultiBinding` et est utilisée comme mode de liaison pour toutes les liaisons de la collection, à moins qu’une liaison individuelle ne substitue la propriété. Par exemple, si la `Mode` propriété d’un `MultiBinding` objet a la valeur [`TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) , toutes les liaisons de la collection sont prises en compte, `TwoWay` sauf si vous définissez explicitement une `Mode` valeur différente sur l’une des liaisons.

## <a name="define-a-imultivalueconverter"></a>Définir un IMultiValueConverter

L' `IMultiValueConverter` interface permet l’application d’une logique personnalisée à un `MultiBinding` . Pour associer un convertisseur à un `MultiBinding` , créez une classe qui implémente l' `IMultiValueConverter` interface, puis implémentez les `Convert` `ConvertBack` méthodes et :

```csharp
public class AllTrueMultiConverter : IMultiValueConverter
{
    public object Convert(object[] values, Type targetType, object parameter, CultureInfo culture)
    {
        if (values == null || !targetType.IsAssignableFrom(typeof(bool)))
        {
            return false;
            // Alternatively, return BindableProperty.UnsetValue to use the binding FallbackValue
        }

        foreach (var value in values)
        {
            if (!(value is bool b))
            {
                return false;
                // Alternatively, return BindableProperty.UnsetValue to use the binding FallbackValue
            }
            else if (!b)
            {
                return false;
            }
        }
        return true;
    }

    public object[] ConvertBack(object value, Type[] targetTypes, object parameter, CultureInfo culture)
    {
        if (!(value is bool b) || targetTypes.Any(t => !t.IsAssignableFrom(typeof(bool))))
        {
            // Return null to indicate conversion back is not possible
            return null;
        }

        if (b)
        {
            return targetTypes.Select(t => (object)true).ToArray();
        }
        else
        {
            // Can't convert back from false because of ambiguity
            return null;
        }
    }
}
```

La `Convert` méthode convertit les valeurs sources en une valeur pour la cible de liaison. Xamarin. Forms appelle cette méthode lorsqu’il propage les valeurs des liaisons sources vers la cible de liaison. Cette méthode accepte quatre arguments :

- `values`, de type `object[]` , est un tableau de valeurs que les liaisons sources dans le `MultiBinding` produisent.
- `targetType`, de type `Type` , est le type de la propriété de cible de liaison.
- `parameter`, de type `object` , est le paramètre de convertisseur à utiliser.
- `culture`, de type `CultureInfo` , est la culture à utiliser dans le convertisseur.

La `Convert` méthode retourne un `object` qui représente une valeur convertie. Cette méthode doit retourner :

- `BindableProperty.UnsetValue` pour indiquer que le convertisseur n’a pas généré de valeur, et que la liaison utilisera `FallbackValue` .
- `Binding.DoNothing` pour ordonner à Xamarin. Forms de ne pas exécuter d’action. Par exemple, pour indiquer à Xamarin. Forms de ne pas transférer une valeur à la cible de liaison, ou de ne pas utiliser le `FallbackValue` .
- `null` pour indiquer que le convertisseur ne peut pas effectuer la conversion et que la liaison utilisera `TargetNullValue` .

> [!IMPORTANT]
> Un `MultiBinding` qui reçoit `BindableProperty.UnsetValue` une `Convert` méthode doit définir sa [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) propriété. De même, un `MultiBinding` qui reçoit `null` d’une `Convert` méthode doit définir sa méthode [`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue) .

La `ConvertBack` méthode convertit une cible de liaison en valeurs de liaison source. Cette méthode accepte quatre arguments :

- `value`, de type `object` , est la valeur produite par la cible de liaison.
- `targetTypes`, de type `Type[]` , est le tableau de types vers lequel effectuer la conversion. La longueur du tableau indique le nombre et les types de valeurs suggérées pour les valeurs retournées par la méthode.
- `parameter`, de type `object` , est le paramètre de convertisseur à utiliser.
- `culture`, de type `CultureInfo` , est la culture à utiliser dans le convertisseur.

La `ConvertBack` méthode retourne un tableau de valeurs, de type `object[]` , qui ont été converties de la valeur cible en valeurs sources. Cette méthode doit retourner :

- `BindableProperty.UnsetValue` à `i` la position pour indiquer que le convertisseur n’est pas en mesure de fournir une valeur pour la liaison source au niveau de l’index `i` et qu’aucune valeur ne doit être définie dessus.
- `Binding.DoNothing` à `i` la position pour indiquer qu’aucune valeur ne doit être définie sur la liaison source au niveau de l’index `i` .
- `null` pour indiquer que le convertisseur ne peut pas effectuer la conversion ou qu’il ne prend pas en charge la conversion dans cette direction.

## <a name="consume-a-imultivalueconverter"></a>Utilisation d’un IMultiValueConverter

Un `IMultiValueConverter` est consommé en l’instanciant dans un dictionnaire de ressources, puis en le référençant à l’aide de l' `StaticResource` extension de balisage pour définir la `MultiBinding.Converter` propriété :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.MultiBindingConverterPage"
             Title="MultiBinding Converter demo">

    <ContentPage.Resources>
        <local:AllTrueMultiConverter x:Key="AllTrueConverter" />
        <local:InverterConverter x:Key="InverterConverter" />
    </ContentPage.Resources>

    <CheckBox>
        <CheckBox.IsChecked>
            <MultiBinding Converter="{StaticResource AllTrueConverter}">
                <Binding Path="Employee.IsOver16" />
                <Binding Path="Employee.HasPassedTest" />
                <Binding Path="Employee.IsSuspended"
                         Converter="{StaticResource InverterConverter}" />
            </MultiBinding>
        </CheckBox.IsChecked>
    </CheckBox>
</ContentPage>    
```

Dans cet exemple, l' `MultiBinding` objet utilise l' `AllTrueMultiConverter` instance pour affecter la valeur à la [`CheckBox.IsChecked`](xref:Xamarin.Forms.CheckBox.IsChecked) propriété `true` , à condition que les trois [`Binding`](xref:Xamarin.Forms.Binding) objets prennent `true` la valeur. Sinon, la `CheckBox.IsChecked` propriété a la valeur `false` .

Par défaut, la [`CheckBox.IsChecked`](xref:Xamarin.Forms.CheckBox.IsChecked) propriété utilise une [`TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) liaison. Par conséquent, la `ConvertBack` méthode de l' `AllTrueMultiConverter` instance est exécutée lorsque la [`CheckBox`](xref:Xamarin.Forms.CheckBox) propriété est désactivée par l’utilisateur, ce qui définit les valeurs de liaison source sur la valeur de la `CheckBox.IsChecked` propriété.

Le code C# équivalent est illustré ci-dessous :

```csharp
public class MultiBindingConverterCodePage : ContentPage
{
    public MultiBindingConverterCodePage()
    {
        BindingContext = new GroupViewModel();

        CheckBox checkBox = new CheckBox();
        checkBox.SetBinding(CheckBox.IsCheckedProperty, new MultiBinding
        {
            Bindings = new Collection<BindingBase>
            {
                new Binding("Employee1.IsOver16"),
                new Binding("Employee1.HasPassedTest"),
                new Binding("Employee1.IsSuspended", converter: new InverterConverter())
            },
            Converter = new AllTrueMultiConverter()
        });

        Title = "MultiBinding converter demo";
        Content = checkBox;
    }
}
```

## <a name="format-strings"></a>Chaînes de format

Un `MultiBinding` peut mettre en forme n’importe quel résultat à plusieurs liaisons qui est affiché sous forme de chaîne, avec la `StringFormat` propriété. Cette propriété peut être définie sur une chaîne de mise en forme .NET standard, avec des espaces réservés, qui spécifie comment mettre en forme le résultat de la liaison multiple :

```xaml
<Label>
    <Label.Text>
        <MultiBinding StringFormat="{}{0} {1} {2}">
            <Binding Path="Employee1.Forename" />
            <Binding Path="Employee1.MiddleName" />
            <Binding Path="Employee1.Surname" />
        </MultiBinding>
    </Label.Text>
</Label>
```

Dans cet exemple, la `StringFormat` propriété combine les trois valeurs liées dans une chaîne unique qui est affichée par le [`Label`](xref:Xamarin.Forms.Label) .

Le code C# équivalent est illustré ci-dessous :

```csharp
Label label = new Label();
label.SetBinding(Label.TextProperty, new MultiBinding
{
    Bindings = new Collection<BindingBase>
    {
        new Binding("Employee1.Forename"),
        new Binding("Employee1.MiddleName"),
        new Binding("Employee1.Surname")
    },
    StringFormat = "{0} {1} {2}"
});
```

> [!IMPORTANT]
> Le nombre de paramètres dans un format de chaîne composite ne peut pas dépasser le nombre d' `Binding` objets enfants dans le `MultiBinding` .

Lorsque vous définissez `Converter` les `StringFormat` Propriétés et, le convertisseur est appliqué en premier à la valeur de données, puis `StringFormat` est appliqué.

Pour plus d’informations sur la mise en forme des chaînes dans Xamarin. Forms, consultez [mise en forme de chaîne Xamarin. Forms](string-formatting.md).

## <a name="provide-fallback-values"></a>Fournir des valeurs de secours

Les liaisons de données peuvent être rendues plus robustes en définissant les valeurs de secours à utiliser en cas d’échec du processus de liaison. Pour ce faire, vous pouvez définir les [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) Propriétés et [`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue) sur un `MultiBinding` objet.

`MultiBinding`Utilise son [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) quand la `Convert` méthode d’une `IMultiValueConverter` instance retourne, ce `BindableProperty.UnsetValue` qui indique que le convertisseur n’a pas généré de valeur. `MultiBinding`Utilise son [`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue) quand la `Convert` méthode d’une `IMultiValueConverter` instance retourne, ce `null` qui indique que le convertisseur ne peut pas effectuer la conversion.

Pour plus d’informations sur la liaison de secours, consultez [Xamarin. Forms liaison Fallback](binding-fallbacks.md).

## <a name="nest-multibinding-objects"></a>Objets multibinds imbriqués

`MultiBinding` les objets peuvent être imbriqués afin que plusieurs `MultiBinding` objets soient évalués pour retourner une valeur par le biais d’une `IMultiValueConverter` instance :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.NestedMultiBindingPage"
             Title="Nested MultiBinding demo">

    <ContentPage.Resources>
        <local:AllTrueMultiConverter x:Key="AllTrueConverter" />
        <local:AnyTrueMultiConverter x:Key="AnyTrueConverter" />
        <local:InverterConverter x:Key="InverterConverter" />
    </ContentPage.Resources>

    <CheckBox>
        <CheckBox.IsChecked>
            <MultiBinding Converter="{StaticResource AnyTrueConverter}">
                <MultiBinding Converter="{StaticResource AllTrueConverter}">
                    <Binding Path="Employee.IsOver16" />
                    <Binding Path="Employee.HasPassedTest" />
                    <Binding Path="Employee.IsSuspended" Converter="{StaticResource InverterConverter}" />                        
                </MultiBinding>
                <Binding Path="Employee.IsMonarch" />
            </MultiBinding>
        </CheckBox.IsChecked>
    </CheckBox>
</ContentPage>
```

Dans cet exemple, l' `MultiBinding` objet utilise son `AnyTrueMultiConverter` instance pour affecter à la propriété la valeur [`CheckBox.IsChecked`](xref:Xamarin.Forms.CheckBox.IsChecked) `true` , à condition que tous les [`Binding`](xref:Xamarin.Forms.Binding) objets de l’objet interne aient la valeur `MultiBinding` `true` , ou à condition que l' `Binding` objet dans l' `MultiBinding` objet externe corresponde à `true` . Sinon, la `CheckBox.IsChecked` propriété a la valeur `false` .

## <a name="use-a-relativesource-binding-in-a-multibinding"></a>Utiliser une liaison RelativeSource dans une MultiBinding

`MultiBinding` les objets prennent en charge les liaisons relatives, qui permettent de définir la source de liaison par rapport à la position de la cible de liaison :

```xaml
<ContentPage ...
             xmlns:local="clr-namespace:DataBindingDemos">
    <ContentPage.Resources>
        <local:AllTrueMultiConverter x:Key="AllTrueConverter" />

        <ControlTemplate x:Key="CardViewExpanderControlTemplate">
            <Expander BindingContext="{Binding Source={RelativeSource TemplatedParent}}"
                      IsExpanded="{Binding IsExpanded, Source={RelativeSource TemplatedParent}}"
                      BackgroundColor="{Binding CardColor}">
                <Expander.IsVisible>
                    <MultiBinding Converter="{StaticResource AllTrueConverter}">
                        <Binding Path="IsExpanded" />
                        <Binding Path="IsEnabled" />
                    </MultiBinding>
                </Expander.IsVisible>
                <Expander.Header>
                    <Grid>
                        <!-- XAML that defines Expander header goes here -->
                    </Grid>
                </Expander.Header>
                <Grid>
                    <!-- XAML that defines Expander content goes here -->
                </Grid>
            </Expander>
        </ControlTemplate>
    </ContentPage.Resources>

    <StackLayout>
        <controls:CardViewExpander BorderColor="DarkGray"
                                   CardTitle="John Doe"
                                   CardDescription="Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla elit dolor, convallis non interdum."
                                   IconBackgroundColor="SlateGray"
                                   IconImageSource="user.png"
                                   ControlTemplate="{StaticResource CardViewExpanderControlTemplate}"
                                   IsEnabled="True"
                                   IsExpanded="True" />
    </StackLayout>
</ContentPage>
```

Dans cet exemple, le `TemplatedParent` mode de liaison relatif est utilisé pour effectuer une liaison à partir d’un modèle de contrôle à l’instance de l’objet d’exécution à laquelle le modèle est appliqué. `Expander`, Qui est l’élément racine du [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) , a son `BindingContext` défini sur l’instance d’objet runtime à laquelle le modèle est appliqué. Par conséquent, le `Expander` et ses enfants résolvent leurs expressions de liaison, ainsi que leurs [`Binding`](xref:Xamarin.Forms.Binding) objets, par rapport aux propriétés de l' `CardViewExpander` objet. `MultiBinding`Utilise l' `AllTrueMultiConverter` instance pour affecter à la propriété la valeur à `Expander.IsVisible` `true` condition que les deux [`Binding`](xref:Xamarin.Forms.Binding) objets prennent la valeur `true` . Sinon, la `Expander.IsVisible` propriété a la valeur `false` .

Pour plus d’informations sur les liaisons relatives, consultez [Liaisons relatives Xamarin.Forms](relative-bindings.md). Pour plus d’informations sur les modèles de contrôle, consultez [modèles de contrôle Xamarin. Forms](~/xamarin-forms/app-fundamentals/templates/control-template.md).

## <a name="related-links"></a>Liens connexes

- [Démonstrations de liaison de données (exemple)](/samples/xamarin/xamarin-forms-samples/databindingdemos)
- [Formatage de chaîne dans Xamarin.Forms](string-formatting.md)
- [Valeurs de repli pour les liaisons Xamarin.Forms](binding-fallbacks.md)
- [Liaisons relatives Xamarin.Forms](relative-bindings.md)
- [Modèles de contrôle Xamarin. Forms](~/xamarin-forms/app-fundamentals/templates/control-template.md)