---
title: Convertisseurs de valeur de liaison Xamarin.Forms
description: Cet article explique comment caster ou convertir des valeurs au sein d’une liaison de données Xamarin.Forms en implémentant un convertisseur de valeurs (également appelé convertisseur de liaison ou convertisseur de valeurs de liaison).
ms.prod: xamarin
ms.assetid: 02B1BBE6-D804-490D-BDD4-8ACED8B70C92
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: 05ad12de77e8895a23cd364b90abfbfb567ac573
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70771619"
---
# <a name="xamarinforms-binding-value-converters"></a>Convertisseurs de valeurs de liaison Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)

Les liaisons de données transfèrent généralement des données d’une propriété source vers une propriété cible et, dans certains cas, de la propriété cible vers la propriété source. Ce transfert est direct lorsque les propriétés source et cible sont du même type, ou quand un type peut être converti vers l’autre type via une conversion implicite. Lorsque ce n’est pas le cas, une conversion de type doit avoir lieu.

Dans l’article [**Formatage de chaîne**](string-formatting.md), vous avez vu comment utiliser la propriété `StringFormat` d’une liaison de données pour convertir n’importe quel type en chaîne. Pour les autres types de conversions, vous devez écrire du code spécialisé dans une classe qui implémente l’interface [`IValueConverter`](xref:Xamarin.Forms.IValueConverter). (La plateforme Windows universelle contient une classe similaire nommée [`IValueConverter`](/uwp/api/Windows.UI.Xaml.Data.IValueConverter/) dans l’espace de noms `Windows.UI.Xaml.Data`, mais ce `IValueConverter` figure dans l’espace de noms `Xamarin.Forms`.) Les classes qui implémentent `IValueConverter` sont appelées *convertisseurs de valeur*, mais elles sont également souvent appelées *convertisseurs de liaison* ou *convertisseurs de valeur de liaison*.

## <a name="the-ivalueconverter-interface"></a>Interface IValueConverter

Supposons que vous souhaitiez définir une liaison de données où la propriété source soit de type `int` mais la propriété cible de type `bool`. Vous souhaitez que cette liaison de données produise une valeur `false` lorsque la source entière est égale à 0, et `true` dans le cas contraire.  

Vous pouvez y parvenir avec une classe qui implémente l'interface `IValueConverter` :

```csharp
public class IntToBoolConverter : IValueConverter
{
    public object Convert(object value, Type targetType, object parameter, CultureInfo culture)
    {
        return (int)value != 0;
    }

    public object ConvertBack(object value, Type targetType, object parameter, CultureInfo culture)
    {
        return (bool)value ? 1 : 0;
    }
}
```

Vous définissez une instance de cette classe sur la propriété [`Converter`](xref:Xamarin.Forms.Binding.Converter) de la classe `Binding` ou sur la propriété [`Converter`](xref:Xamarin.Forms.Xaml.BindingExtension.Converter) de l’extension de balisage `Binding`. Cette classe devient partie intégrante de la liaison de données.

La méthode `Convert` est appelée lorsque les données sont transférées de la source vers la cible dans des liaisons `OneWay` ou `TwoWay`. Le paramètre `value` correspond à l’objet ou la valeur de la source de liaison de données. La méthode doit retourner une valeur du type de la cible de liaison de données. La méthode illustrée ici caste le paramètre `value` en `int` puis le compare à 0 pour une valeur renvoyée `bool`.

La méthode `ConvertBack` est appelée lorsque les données sont transférées de la cible vers la source dans des liaisons `TwoWay` ou `OneWayToSource`. `ConvertBack` effectue la conversion inverse : Il suppose que le paramètre `value` est un `bool` issu de la cible et le convertit en une valeur renvoyée `int` pour la source.

Si la liaison de données inclut également un paramètre `StringFormat`, le convertisseur de valeurs est appelé avant que le résultat soit formaté en tant que chaîne.

La page **Enable Buttons** (Activer les boutons) dans l’exemple [**Data Binding Demos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos) (Démos des liaisons de données) illustre l’utilisation de ce convertisseur de valeurs dans une liaison de données. `IntToBoolConverter` est instancié dans le dictionnaire de ressources de la page. Il est ensuite référencé avec une extension de balisage `StaticResource` pour définir la propriété `Converter` dans deux liaisons de données. Il est très courant de partager des convertisseurs de données entre plusieurs liaisons de données sur la page :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.EnableButtonsPage"
             Title="Enable Buttons">
    <ContentPage.Resources>
        <ResourceDictionary>
            <local:IntToBoolConverter x:Key="intToBool" />
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout Padding="10, 0">
        <Entry x:Name="entry1"
               Text=""
               Placeholder="enter search term"
               VerticalOptions="CenterAndExpand" />

        <Button Text="Search"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                IsEnabled="{Binding Source={x:Reference entry1},
                                    Path=Text.Length,
                                    Converter={StaticResource intToBool}}" />

        <Entry x:Name="entry2"
               Text=""
               Placeholder="enter destination"
               VerticalOptions="CenterAndExpand" />

        <Button Text="Submit"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                IsEnabled="{Binding Source={x:Reference entry2},
                                    Path=Text.Length,
                                    Converter={StaticResource intToBool}}" />
    </StackLayout>
</ContentPage>
```

Si un convertisseur de valeurs est utilisé dans plusieurs pages de votre application, vous pouvez l’instancier dans le dictionnaire de ressources, dans le fichier **App.xaml**.

La page **Enable Buttons** (Activer les boutons) illustre un besoin commun quand un `Button` effectue une opération basée sur du texte que l’utilisateur tape dans une vue `Entry`. Si rien n’a été saisi dans `Entry`, l’élément `Button` doit être désactivé. Chaque élément `Button` contient une liaison de données sur sa propriété `IsEnabled`. La source de liaison de données est la propriété `Length` de la propriété `Text` de l’élément `Entry` correspondant. Si cette propriété `Length` n’est pas égale à 0, le convertisseur de valeurs retourne `true` et le `Button` est activé :

[![Enable Buttons](converters-images/enablebuttons-small.png "Enable Buttons")](converters-images/enablebuttons-large.png#lightbox "Enable Buttons")

Notez que la propriété `Text` dans chaque `Entry` est initialisée en tant que chaîne vide. La propriété `Text` est `null` par défaut et la liaison de données ne fonctionne pas dans ce cas.

Certains convertisseurs de valeur sont écrits spécifiquement pour des applications spécifiques, tandis que d’autres sont généralisées. Si vous savez qu’un convertisseur de valeurs servira uniquement dans des liaisons `OneWay`, la méthode `ConvertBack` peut retourner simplement `null`.

La méthode `Convert` illustrée ci-dessus suppose implicitement que l’argument `value` est de type `int` et que la valeur renvoyée doit être de type `bool`. De même, la méthode `ConvertBack` suppose que l’argument `value` est de type `bool` et que la valeur renvoyée est de type `int`. Si tel n’est pas le cas, une exception runtime se produit.

Vous pouvez écrire des convertisseurs de valeur plus généralisés qui acceptent plusieurs types de données différents. Les méthodes `Convert` et `ConvertBack` peuvent utiliser les opérateurs `as` et `is` avec le paramètre `value`, ou peuvent appeler `GetType` sur ce paramètre pour déterminer son type, puis prendre une mesure appropriée. Le type attendu de la valeur renvoyée de chaque méthode est fourni par le paramètre `targetType`. Parfois, les convertisseurs de valeur sont utilisés avec des liaisons de données de différents types de cible ; le convertisseur de valeurs peut utiliser l’argument `targetType` pour effectuer une conversion vers le type correct.

Si la conversion effectuée est différente pour différentes cultures, utilisez le paramètre `culture` à cet effet. L’argument `parameter` de `Convert` et `ConvertBack` est abordé plus loin dans cet article.

## <a name="binding-converter-properties"></a>Propriétés des convertisseurs de liaison

Les classes de convertisseur de valeurs peuvent avoir des propriétés et des paramètres génériques. Ce convertisseur de valeurs particulier convertit un `bool` à partir de la source vers un objet de type `T` pour la cible :

```csharp
public class BoolToObjectConverter<T> : IValueConverter
{
    public T TrueObject { set; get; }

    public T FalseObject { set; get; }

    public object Convert(object value, Type targetType, object parameter, CultureInfo culture)
    {
        return (bool)value ? TrueObject : FalseObject;
    }

    public object ConvertBack(object value, Type targetType, object parameter, CultureInfo culture)
    {
        return ((T)value).Equals(TrueObject);
    }
}
```

La page **Switch Indicators** (Indicateurs de commutateur) montre comment l’utiliser pour afficher la valeur d’une vue `Switch`. Bien qu’il soit courant d’instancier des convertisseurs de valeur comme des ressources dans un dictionnaire de ressources, cette page montre une alternative : Chaque convertisseur de valeurs est instancié entre des balises d’élément de propriété `Binding.Converter`. `x:TypeArguments` indique l’argument générique, et `TrueObject` et `FalseObject` sont tous les deux définis sur des objets de ce type :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.SwitchIndicatorsPage"
             Title="Switch Indicators">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Label">
                <Setter Property="FontSize" Value="18" />
                <Setter Property="VerticalOptions" Value="Center" />
            </Style>

            <Style TargetType="Switch">
                <Setter Property="VerticalOptions" Value="Center" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout Padding="10, 0">
        <StackLayout Orientation="Horizontal"
                     VerticalOptions="CenterAndExpand">
            <Label Text="Subscribe?" />
            <Switch x:Name="switch1" />
            <Label>
                <Label.Text>
                    <Binding Source="{x:Reference switch1}"
                             Path="IsToggled">
                        <Binding.Converter>
                            <local:BoolToObjectConverter x:TypeArguments="x:String"
                                                         TrueObject="Of course!"
                                                         FalseObject="No way!" />
                        </Binding.Converter>
                    </Binding>
                </Label.Text>
            </Label>
        </StackLayout>

        <StackLayout Orientation="Horizontal"
                     VerticalOptions="CenterAndExpand">
            <Label Text="Allow popups?" />
            <Switch x:Name="switch2" />
            <Label>
                <Label.Text>
                    <Binding Source="{x:Reference switch2}"
                             Path="IsToggled">
                        <Binding.Converter>
                            <local:BoolToObjectConverter x:TypeArguments="x:String"
                                                         TrueObject="Yes"
                                                         FalseObject="No" />
                        </Binding.Converter>
                    </Binding>
                </Label.Text>
                <Label.TextColor>
                    <Binding Source="{x:Reference switch2}"
                             Path="IsToggled">
                        <Binding.Converter>
                            <local:BoolToObjectConverter x:TypeArguments="Color"
                                                         TrueObject="Green"
                                                         FalseObject="Red" />
                        </Binding.Converter>
                    </Binding>
                </Label.TextColor>
            </Label>
        </StackLayout>

        <StackLayout Orientation="Horizontal"
                     VerticalOptions="CenterAndExpand">
            <Label Text="Learn more?" />
            <Switch x:Name="switch3" />
            <Label FontSize="18"
                   VerticalOptions="Center">
                <Label.Style>
                    <Binding Source="{x:Reference switch3}"
                             Path="IsToggled">
                        <Binding.Converter>
                            <local:BoolToObjectConverter x:TypeArguments="Style">
                                <local:BoolToObjectConverter.TrueObject>
                                    <Style TargetType="Label">
                                        <Setter Property="Text" Value="Indubitably!" />
                                        <Setter Property="FontAttributes" Value="Italic, Bold" />
                                        <Setter Property="TextColor" Value="Green" />
                                    </Style>                                    
                                </local:BoolToObjectConverter.TrueObject>

                                <local:BoolToObjectConverter.FalseObject>
                                    <Style TargetType="Label">
                                        <Setter Property="Text" Value="Maybe later" />
                                        <Setter Property="FontAttributes" Value="None" />
                                        <Setter Property="TextColor" Value="Red" />
                                    </Style>
                                </local:BoolToObjectConverter.FalseObject>
                            </local:BoolToObjectConverter>
                        </Binding.Converter>
                    </Binding>
                </Label.Style>
            </Label>
        </StackLayout>
    </StackLayout>
</ContentPage>
```

Dans la dernière des trois paires de `Switch` et `Label`, l’argument générique a la valeur `Style` et des objets `Style` entiers sont fournis pour les valeurs de `TrueObject` et `FalseObject`. Ceux-ci remplacent le style implicite pour `Label`, défini dans le dictionnaire de ressources, afin que les propriétés figurant dans ce style soient explicitement affectées à l’objet `Label`. Lors de l’activation/la désactivation de `Switch`, l’objet `Label` correspondant reflète la modification :

[![Switch Indicators](converters-images/switchindicators-small.png "Switch Indicators")](converters-images/switchindicators-large.png#lightbox "Switch Indicators")

Il est également possible d’utiliser [`Triggers`](~/xamarin-forms/app-fundamentals/triggers.md) pour implémenter des modifications similaires dans l’interface utilisateur sur la base d’autres vues.

## <a name="binding-converter-parameters"></a>Paramètres des convertisseurs de liaison

La classe `Binding` définit une propriété [`ConverterParameter`](xref:Xamarin.Forms.Binding.ConverterParameter) et l’extension de balisage `Binding` définit également une propriété [`ConverterParameter`](xref:Xamarin.Forms.Xaml.BindingExtension.ConverterParameter). Si cette propriété est définie, la valeur est transmise aux méthodes `Convert` et `ConvertBack` en tant qu’argument `parameter`. Même si l’instance du convertisseur de valeurs est partagée entre plusieurs liaisons de données, l’élément `ConverterParameter` peut être différent pour effectuer des conversions quelque peu différentes.

L’utilisation de `ConverterParameter` est illustrée avec un programme de sélection de couleur. Dans ce cas, l’élément `RgbColorViewModel` a trois propriétés de type `double` nommées `Red`, `Green` et `Blue`, qu’il utilise pour construire une valeur `Color` :

```csharp
public class RgbColorViewModel : INotifyPropertyChanged
{
    Color color;
    string name;

    public event PropertyChangedEventHandler PropertyChanged;

    public double Red
    {
        set
        {
            if (color.R != value)
            {
                Color = new Color(value, color.G, color.B);
            }
        }
        get
        {
            return color.R;
        }
    }

    public double Green
    {
        set
        {
            if (color.G != value)
            {
                Color = new Color(color.R, value, color.B);
            }
        }
        get
        {
            return color.G;
        }
    }

    public double Blue
    {
        set
        {
            if (color.B != value)
            {
                Color = new Color(color.R, color.G, value);
            }
        }
        get
        {
            return color.B;
        }
    }

    public Color Color
    {
        set
        {
            if (color != value)
            {
                color = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Red"));
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Green"));
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Blue"));
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Color"));

                Name = NamedColor.GetNearestColorName(color);
            }
        }
        get
        {
            return color;
        }
    }

    public string Name
    {
        private set
        {
            if (name != value)
            {
                name = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Name"));
            }
        }
        get
        {
            return name;
        }
    }
}
```

Les propriétés `Red`, `Green` et `Blue` sont comprises entre 0 et 1. Toutefois, vous préférerez peut-être que les composants soient affichés en tant que valeurs hexadécimales à deux chiffres.

Pour les afficher en tant que valeurs hexadécimales en XAML, ils doivent être multipliés par 255, convertis en entiers puis formatés avec une spécification de « X2 » dans la propriété `StringFormat`. Les deux premières tâches (multiplication par 255 et conversion en entier) peuvent être gérées par le convertisseur de valeur. Pour généraliser le plus possible le convertisseur de valeur, le facteur de multiplication peut être spécifié avec la propriété `ConverterParameter`, ce qui signifie qu’il entre les méthodes `Convert` et `ConvertBack` en tant qu’argument `parameter` :

```csharp
public class DoubleToIntConverter : IValueConverter
{
    public object Convert(object value, Type targetType, object parameter, CultureInfo culture)
    {
        return (int)Math.Round((double)value * GetParameter(parameter));
    }

    public object ConvertBack(object value, Type targetType, object parameter, CultureInfo culture)
    {
        return (int)value / GetParameter(parameter);
    }

    double GetParameter(object parameter)
    {
        if (parameter is double)
            return (double)parameter;

        else if (parameter is int)
            return (int)parameter;

        else if (parameter is string)
            return double.Parse((string)parameter);

        return 1;
    }
}
```

`Convert` convertit un `double` en `int` tout en le multipliant par la valeur `parameter` ; `ConvertBack` divise l’argument `value` entier par `parameter` et retourne un résultat `double`. (Dans le programme illustré ci-dessous, le convertisseur de valeurs est utilisé uniquement en rapport avec le formatage de chaîne, si bien que `ConvertBack` n’est pas utilisé.)

Le type de l’argument `parameter` est susceptible de varier selon que la liaison de données est définie dans le code ou en XAML. Si la propriété `ConverterParameter` de `Binding` est définie dans le code, l’argument est probablement défini sur une valeur numérique :

```csharp
binding.ConverterParameter = 255;
```

La propriété `ConverterParameter` est de type `Object`, si bien que le compilateur C# interprète le littéral 255 comme un entier et définit la propriété sur cette valeur.

En XAML, toutefois, l’élément `ConverterParameter` sera probablement défini comme suit :

```xaml
<Label Text="{Binding Red,
                      Converter={StaticResource doubleToInt},
                      ConverterParameter=255,
                      StringFormat='Red = {0:X2}'}" />
```

L’élément 255 ressemble à un nombre, mais comme `ConverterParameter` est de type `Object`, l’analyseur XAML traite 255 en tant que chaîne.

Pour cette raison, le convertisseur de valeurs illustré ci-dessus inclut une méthode `GetParameter` distincte qui gère des cas où `parameter` est de type `double`, `int` ou `string`.  

La page **RGB Color Selector** (Sélecteur de couleur RVB) instancie `DoubleToIntConverter` dans son dictionnaire de ressources qui suit la définition de deux styles implicites :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.RgbColorSelectorPage"
             Title="RGB Color Selector">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Slider">
                <Setter Property="VerticalOptions" Value="CenterAndExpand" />
            </Style>

            <Style TargetType="Label">
                <Setter Property="HorizontalTextAlignment" Value="Center" />
            </Style>

            <local:DoubleToIntConverter x:Key="doubleToInt" />
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout>
        <StackLayout.BindingContext>
            <local:RgbColorViewModel Color="Gray" />
        </StackLayout.BindingContext>

        <BoxView Color="{Binding Color}"
                 VerticalOptions="FillAndExpand" />

        <StackLayout Margin="10, 0">
            <Label Text="{Binding Name}" />

            <Slider Value="{Binding Red}" />
            <Label Text="{Binding Red,
                                  Converter={StaticResource doubleToInt},
                                  ConverterParameter=255,
                                  StringFormat='Red = {0:X2}'}" />

            <Slider Value="{Binding Green}" />
            <Label Text="{Binding Green,
                                  Converter={StaticResource doubleToInt},
                                  ConverterParameter=255,
                                  StringFormat='Green = {0:X2}'}" />

            <Slider Value="{Binding Blue}" />
            <Label>
                <Label.Text>
                    <Binding Path="Blue"
                             StringFormat="Blue = {0:X2}"
                             Converter="{StaticResource doubleToInt}">
                        <Binding.ConverterParameter>
                            <x:Double>255</x:Double>
                        </Binding.ConverterParameter>
                    </Binding>
                </Label.Text>
            </Label>
        </StackLayout>
    </StackLayout>
</ContentPage>    
```

Les valeurs des propriétés `Red` et `Green` sont affichées avec une extension de balisage `Binding`. La propriété `Blue`, toutefois, instancie la classe `Binding` afin d’illustrer comment une valeur `double` explicite peut être définie sur la propriété `ConverterParameter`.

Voici le résultat :

[![RGB Color Selector](converters-images/rgbcolorselector-small.png "RGB Color Selector")](converters-images/rgbcolorselector-large.png#lightbox "RGB Color Selector")

## <a name="related-links"></a>Liens associés

- [Démos des liaisons de données (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)
- [Chapitre sur les liaisons de données dans la documentation de Xamarin.Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
