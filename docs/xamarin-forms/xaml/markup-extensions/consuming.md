---
title: Consommation des extensions de balisage XAML
description: Cet article explique comment utiliser les extensions de balisage XAML Xamarin. Forms pour améliorer la puissance et la flexibilité du code XAML en permettant de définir des attributs d’élément à partir de diverses sources.
ms.prod: xamarin
ms.assetid: CE686893-609C-4EC3-9225-6C68D2A9F79C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/21/2020
ms.openlocfilehash: 7f46bc84543a1838241923ab91809bd01c706f44
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82517111"
---
# <a name="consuming-xaml-markup-extensions"></a>Consommation des extensions de balisage XAML

[![Télécharger l'](~/media/shared/download.png) exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-markupextensions)

Les extensions de balisage XAML aident à améliorer la puissance et la flexibilité du XAML en permettant de définir des attributs d’élément à partir de diverses sources. Plusieurs extensions de balisage XAML font partie de la spécification XAML 2009. Ils s’affichent dans les fichiers XAML avec `x` le préfixe d’espace de noms personnalisé, et sont communément désignés par ce préfixe. Cet article aborde les extensions de balisage suivantes :

- [`x:Static`](#static): référencez des propriétés statiques, des champs ou des membres d’énumération.
- [`x:Reference`](#reference): référence des éléments nommés sur la page.
- [`x:Type`](#type): définissez un attribut sur un `System.Type` objet.
- [`x:Array`](#array): construisez un tableau d’objets d’un type particulier.
- [`x:Null`](#null): affectez une `null` valeur à un attribut.
- [`OnPlatform`](#onplatform): personnaliser l’apparence de l’interface utilisateur sur la base de chaque plateforme.
- [`OnIdiom`](#onidiom): personnalisez l’apparence de l’interface utilisateur en fonction de l’idiome du périphérique sur lequel l’application s’exécute.
- [`DataTemplate`](#datatemplate-markup-extension): convertit un type en [`DataTemplate`](xref:Xamarin.Forms.DataTemplate).
- [`FontImage`](#fontimage-markup-extension): affiche une icône de police dans n’importe quelle vue pouvant `ImageSource`afficher un.
- [`OnAppTheme`](#onapptheme-markup-extension): utilise une ressource basée sur le thème système actuel.

Des extensions de balisage XAML supplémentaires ont été prises en charge par d’autres implémentations XAML, et sont également prises en charge par Xamarin. Forms. Celles-ci sont décrites plus en détail dans d’autres articles :

- `StaticResource`-référencez des objets à partir d’un dictionnaire de ressources, comme décrit dans l’article [**dictionnaires de ressources**](~/xamarin-forms/xaml/resource-dictionaries.md).
- `DynamicResource`-répondre aux modifications apportées aux objets dans un dictionnaire de ressources, comme décrit dans l’article [**styles dynamiques**](~/xamarin-forms/user-interface/styles/dynamic.md).
- `Binding`: établissez un lien entre les propriétés de deux objets, comme décrit dans l’article [**liaison de données**](~/xamarin-forms/app-fundamentals/data-binding/index.md).
- `TemplateBinding`-effectue une liaison de données à partir d’un modèle de contrôle, comme indiqué dans l’article [**modèles de contrôle Xamarin. Forms**](~/xamarin-forms/app-fundamentals/templates/control-template.md).
- `RelativeSource`-définit la source de liaison par rapport à la position de la cible de liaison, comme indiqué dans l’article [liaisons relatives](~/xamarin-forms/app-fundamentals/data-binding/relative-bindings.md).

La [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) disposition utilise l’extension [`ConstraintExpression`](xref:Xamarin.Forms.ConstraintExpression)de balisage personnalisée. Cette extension de balisage est décrite dans l’article [**RelativeLayout**](~/xamarin-forms/user-interface/layouts/relative-layout.md).

<a name="static" />

## <a name="xstatic-markup-extension"></a>x:Static (extension de balisage)

L' `x:Static` extension de balisage est prise [`StaticExtension`](xref:Xamarin.Forms.Xaml.StaticExtension) en charge par la classe. La classe a une propriété unique nommée [`Member`](xref:Xamarin.Forms.Xaml.StaticExtension.Member) de type `string` que vous définissez sur le nom d’une constante publique, d’une propriété statique, d’un champ statique ou d’un membre d’énumération.

Une façon courante d’utiliser `x:Static` est de commencer par définir une classe avec des constantes ou des variables statiques, telles `AppConstants` que cette petite classe dans le programme [**MarkupExtensions**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-markupextensions) :

```csharp
static class AppConstants
{
    public static double NormalFontSize = 18;
}
```

La page de **démonstration de x :static** montre plusieurs façons d' `x:Static` utiliser l’extension de balisage. L’approche la plus détaillée instancie la `StaticExtension` classe entre `Label.FontSize` les balises d’éléments de propriété :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:sys="clr-namespace:System;assembly=netstandard"
             xmlns:local="clr-namespace:MarkupExtensions"
             x:Class="MarkupExtensions.StaticDemoPage"
             Title="x:Static Demo">
    <StackLayout Margin="10, 0">
        <Label Text="Label No. 1">
            <Label.FontSize>
                <x:StaticExtension Member="local:AppConstants.NormalFontSize" />
            </Label.FontSize>
        </Label>

        ···

    </StackLayout>
</ContentPage>
```

L’analyseur XAML permet également à `StaticExtension` la classe d’être abrégée `x:Static`comme suit :

```xaml
<Label Text="Label No. 2">
    <Label.FontSize>
        <x:Static Member="local:AppConstants.NormalFontSize" />
    </Label.FontSize>
</Label>
```

Cela peut être simplifié encore plus, mais la modification introduit une nouvelle syntaxe : elle consiste à placer la `StaticExtension` classe et le paramètre de membre entre accolades. L’expression résultante est définie directement sur `FontSize` l’attribut :

```xaml
<Label Text="Label No. 3"
       FontSize="{x:StaticExtension Member=local:AppConstants.NormalFontSize}" />
```

Notez qu’il n’y a *pas* de guillemets à l’intérieur des accolades. La `Member` propriété de `StaticExtension` n’est plus un attribut XML. Au lieu de cela, elle fait partie de l’expression de l’extension de balisage.

Tout comme vous pouvez l' `x:StaticExtension` abréger `x:Static` quand vous l’utilisez en tant qu’élément objet, vous pouvez également l’abréger dans l’expression entre accolades :

```xaml
<Label Text="Label No. 4"
       FontSize="{x:Static Member=local:AppConstants.NormalFontSize}" />
```

La `StaticExtension` classe a un `ContentProperty` attribut qui référence la `Member`propriété, qui marque cette propriété comme la propriété de contenu par défaut de la classe. Pour les extensions de balisage XAML exprimées à l’aide d’accolades `Member=` , vous pouvez éliminer la partie de l’expression :

```xaml
<Label Text="Label No. 5"
       FontSize="{x:Static local:AppConstants.NormalFontSize}" />
```

Il s’agit de la forme la plus `x:Static` courante de l’extension de balisage.

La page de **démonstration statique** contient deux autres exemples. La balise racine du fichier XAML contient une déclaration d’espace de noms XML `System` pour l’espace de noms .net :

```xaml
xmlns:sys="clr-namespace:System;assembly=netstandard"
```

Cela permet de `Label` définir la taille de police sur le champ `Math.PI`statique. Cela donne lieu à du texte plutôt petit, `Scale` donc la propriété est `Math.E`définie sur :

```xaml
<Label Text="&#x03C0; &#x00D7; E sized text"
       FontSize="{x:Static sys:Math.PI}"
       Scale="{x:Static sys:Math.E}"
       HorizontalOptions="Center" />
```

Le dernier exemple affiche la `Device.RuntimePlatform` valeur. La `Environment.NewLine` propriété statique est utilisée pour insérer un caractère de nouvelle ligne entre les deux `Span` objets :

```xaml
<Label HorizontalTextAlignment="Center"
       FontSize="{x:Static local:AppConstants.NormalFontSize}">
    <Label.FormattedText>
        <FormattedString>
            <Span Text="Runtime Platform: " />
            <Span Text="{x:Static sys:Environment.NewLine}" />
            <Span Text="{x:Static Device.RuntimePlatform}" />
        </FormattedString>
    </Label.FormattedText>
</Label>
```

Voici l’exemple en cours d’exécution :

[![Démonstration de x :Static](consuming-images/staticdemo-small.png "Démonstration de x :Static")](consuming-images/staticdemo-large.png#lightbox "Démonstration de x :Static")

<a name="reference" />

## <a name="xreference-markup-extension"></a>x :Reference (extension de balisage)

L' `x:Reference` extension de balisage est prise [`ReferenceExtension`](xref:Xamarin.Forms.Xaml.ReferenceExtension) en charge par la classe. La classe a une propriété unique nommée [`Name`](xref:Xamarin.Forms.Xaml.ReferenceExtension.Name) de type `string` que vous définissez sur le nom d’un élément de la page auquel un nom a été attribué `x:Name`. Cette `Name` propriété est la propriété de contenu `ReferenceExtension`de, `Name=` ce qui n’est `x:Reference` pas obligatoire lorsque s’affiche entre accolades.

L' `x:Reference` extension de balisage est utilisée exclusivement avec les liaisons de données, qui sont décrites plus en détail dans l’article [**liaison de données**](~/xamarin-forms/app-fundamentals/data-binding/index.md).

La page de **démonstration** de la fonction x :Reference `x:Reference` montre deux utilisations de avec les liaisons de données, la première où elle `Source` est utilisée pour `Binding` définir la propriété de l’objet, et la seconde où elle `BindingContext` est utilisée pour définir la propriété pour deux liaisons de données :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="MarkupExtensions.ReferenceDemoPage"
             x:Name="page"
             Title="x:Reference Demo">

    <StackLayout Margin="10, 0">

        <Label Text="{Binding Source={x:Reference page},
                              StringFormat='The type of this page is {0}'}"
               FontSize="18"
               VerticalOptions="CenterAndExpand"
               HorizontalTextAlignment="Center" />

        <Slider x:Name="slider"
                Maximum="360"
                VerticalOptions="Center" />

        <Label BindingContext="{x:Reference slider}"
               Text="{Binding Value, StringFormat='{0:F0}&#x00B0; rotation'}"
               Rotation="{Binding Value}"
               FontSize="24"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

    </StackLayout>
</ContentPage>
```

Les `x:Reference` deux expressions utilisent la version abrégée du `ReferenceExtension` nom de la classe et `Name=` éliminent la partie de l’expression. Dans le premier exemple, l' `x:Reference` extension de balisage est incorporée dans l' `Binding` extension de balisage. Notez que les `Source` paramètres `StringFormat` et sont séparés par des virgules. Voici le programme en cours d’exécution :

[![Démonstration de la x :Reference](consuming-images/referencedemo-small.png "Démonstration de la x :Reference")](consuming-images/referencedemo-large.png#lightbox "Démonstration de la x :Reference")

<a name="type" />

## <a name="xtype-markup-extension"></a>x:Type (extension de balisage)

L' `x:Type` extension de balisage est l’équivalent XAML du [`typeof`](/dotnet/csharp/language-reference/keywords/typeof/) mot clé C#. Elle est prise en charge [`TypeExtension`](xref:Xamarin.Forms.Xaml.TypeExtension) par la classe, qui définit une [`TypeName`](xref:Xamarin.Forms.Xaml.TypeExtension.TypeName) propriété nommée `string` de type qui a pour valeur un nom de classe ou de structure. L' `x:Type` extension de balisage [`System.Type`](xref:System.Type) retourne l’objet de cette classe ou structure. `TypeName`est la propriété de contenu `TypeExtension`de, `TypeName=` ce qui n’est `x:Type` pas obligatoire lorsque s’affiche entre accolades.

Dans Xamarin. Forms, il existe plusieurs propriétés qui ont des arguments `Type`de type. Il peut s' [`TargetType`](xref:Xamarin.Forms.Style.TargetType) agir, `Style`par exemple, de la propriété de et de l’attribut [x :TypeArguments](~/xamarin-forms/xaml/passing-arguments.md#generic_type_arguments) utilisé pour spécifier des arguments dans des classes génériques. Toutefois, l’analyseur XAML effectue l' `typeof` opération automatiquement et l' `x:Type` extension de balisage n’est pas utilisée dans ces cas.

L’extension de `x:Type` `x:Array` balisage, qui est décrite dans la [section suivante](#array) *, est un emplacement requis.*

L' `x:Type` extension de balisage est également utile lors de la construction d’un menu où chaque élément de menu correspond à un objet d’un type particulier. Vous pouvez associer un `Type` objet à chaque élément de menu, puis instancier l’objet quand l’élément de menu est sélectionné.

Voici comment fonctionne le menu de navigation `MainPage` dans dans le programme des **extensions de balisage** . Le fichier **MainPage. Xaml** contient un `TableView` avec chaque `TextCell` correspondant à une page particulière dans le programme :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:MarkupExtensions"
             x:Class="MarkupExtensions.MainPage"
             Title="Markup Extensions"
             Padding="10">
    <TableView Intent="Menu">
        <TableRoot>
            <TableSection>
                <TextCell Text="x:Static Demo"
                          Detail="Access constants or statics"
                          Command="{Binding NavigateCommand}"
                          CommandParameter="{x:Type local:StaticDemoPage}" />

                <TextCell Text="x:Reference Demo"
                          Detail="Reference named elements on the page"
                          Command="{Binding NavigateCommand}"
                          CommandParameter="{x:Type local:ReferenceDemoPage}" />

                <TextCell Text="x:Type Demo"
                          Detail="Associate a Button with a Type"
                          Command="{Binding NavigateCommand}"
                          CommandParameter="{x:Type local:TypeDemoPage}" />

                <TextCell Text="x:Array Demo"
                          Detail="Use an array to fill a ListView"
                          Command="{Binding NavigateCommand}"
                          CommandParameter="{x:Type local:ArrayDemoPage}" />

                ···                          

        </TableRoot>
    </TableView>
</ContentPage>
```

Voici la page principale d’ouverture dans les **extensions de balisage**:

[![Page principale](consuming-images/mainpage-small.png "Page principale")](consuming-images/mainpage-large.png#lightbox "Page principale")

Chaque `CommandParameter` propriété est définie sur une `x:Type` extension de balisage qui fait référence à l’une des autres pages. La `Command` propriété est liée à une propriété nommée `NavigateCommand`. Cette propriété est définie dans le `MainPage` fichier code-behind :

```csharp
public partial class MainPage : ContentPage
{
    public MainPage()
    {
        InitializeComponent();

        NavigateCommand = new Command<Type>(async (Type pageType) =>
        {
            Page page = (Page)Activator.CreateInstance(pageType);
            await Navigation.PushAsync(page);
        });

        BindingContext = this;
    }

    public ICommand NavigateCommand { private set; get; }
}
```

La `NavigateCommand` propriété est un `Command` objet qui implémente une commande Execute avec un argument de `Type` &mdash; type la valeur `CommandParameter`. La méthode utilise `Activator.CreateInstance` pour instancier la page, puis y accède. Le constructeur conclut en affectant à `BindingContext` la valeur de la page elle-même, `Binding` ce `Command` qui permet au de fonctionner. Pour plus d’informations sur ce type de code, consultez l’article sur la [**liaison de données**](~/xamarin-forms/app-fundamentals/data-binding/index.md) et en particulier l’article sur les [**commandes**](~/xamarin-forms/app-fundamentals/data-binding/commanding.md) .

La page de **démonstration x :type** utilise une technique similaire pour instancier des éléments Xamarin. Forms et les `StackLayout`ajouter à un. Le fichier XAML se compose initialement de `Button` trois éléments dont `Command` les propriétés sont définies `Binding` sur a `CommandParameter` et les propriétés définies sur types de trois vues Xamarin. Forms :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="MarkupExtensions.TypeDemoPage"
             Title="x:Type Demo">

    <StackLayout x:Name="stackLayout"
                 Padding="10, 0">

        <Button Text="Create a Slider"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                Command="{Binding CreateCommand}"
                CommandParameter="{x:Type Slider}" />

        <Button Text="Create a Stepper"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                Command="{Binding CreateCommand}"
                CommandParameter="{x:Type Stepper}" />

        <Button Text="Create a Switch"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                Command="{Binding CreateCommand}"
                CommandParameter="{x:Type Switch}" />
    </StackLayout>
</ContentPage>
```

Le fichier code-behind définit et initialise la `CreateCommand` propriété :

```csharp
public partial class TypeDemoPage : ContentPage
{
    public TypeDemoPage()
    {
        InitializeComponent();

        CreateCommand = new Command<Type>((Type viewType) =>
        {
            View view = (View)Activator.CreateInstance(viewType);
            view.VerticalOptions = LayoutOptions.CenterAndExpand;
            stackLayout.Children.Add(view);
        });

        BindingContext = this;
    }

    public ICommand CreateCommand { private set; get; }
}
```

La méthode qui est exécutée quand `Button` un est enfoncé crée une nouvelle instance de l’argument, définit `VerticalOptions` sa propriété et l' `StackLayout`ajoute à. Les trois `Button` éléments partagent ensuite la page avec des vues créées dynamiquement :

[![Démonstration de x :Type](consuming-images/typedemo-small.png "Démonstration de x :Type")](consuming-images/typedemo-large.png#lightbox "Démonstration de x :Type")

<a name="array" />

## <a name="xarray-markup-extension"></a>x:Array (extension de balisage)

L' `x:Array` extension de balisage vous permet de définir un tableau dans le balisage. Elle est prise en charge [`ArrayExtension`](xref:Xamarin.Forms.Xaml.ArrayExtension) par la classe, qui définit deux propriétés :

- `Type`de type `Type`, qui indique le type des éléments dans le tableau.
- `Items`de type `IList`, qui est une collection d’éléments eux-mêmes. Il s’agit de la propriété `ArrayExtension`de contenu de.

L' `x:Array` extension de balisage elle-même n’apparaît jamais entre accolades. Au lieu `x:Array` de cela, les balises de début et de fin délimitent la liste des éléments. Affectez `Type` à la propriété `x:Type` une extension de balisage.

La page de **démonstration x :Array** montre comment utiliser `x:Array` pour ajouter des éléments à `ListView` un en affectant à la `ItemsSource` propriété un tableau :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="MarkupExtensions.ArrayDemoPage"
             Title="x:Array Demo Page">
    <ListView Margin="10">
        <ListView.ItemsSource>
            <x:Array Type="{x:Type Color}">
                <Color>Aqua</Color>
                <Color>Black</Color>
                <Color>Blue</Color>
                <Color>Fuchsia</Color>
                <Color>Gray</Color>
                <Color>Green</Color>
                <Color>Lime</Color>
                <Color>Maroon</Color>
                <Color>Navy</Color>
                <Color>Olive</Color>
                <Color>Pink</Color>
                <Color>Purple</Color>
                <Color>Red</Color>
                <Color>Silver</Color>
                <Color>Teal</Color>
                <Color>White</Color>
                <Color>Yellow</Color>
            </x:Array>
        </ListView.ItemsSource>

        <ListView.ItemTemplate>
            <DataTemplate>
                <ViewCell>
                    <BoxView Color="{Binding}"
                             Margin="3" />    
                </ViewCell>
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
</ContentPage>        
```

Le `ViewCell` crée un simple `BoxView` pour chaque entrée de couleur :

[![Démo x :Array](consuming-images/arraydemo-small.png "Démo x :Array")](consuming-images/arraydemo-large.png#lightbox "Démo x :Array")

Il existe plusieurs façons de spécifier les éléments `Color` individuels dans ce tableau. Vous pouvez utiliser une `x:Static` extension de balisage :

```xaml
<x:Static Member="Color.Blue" />
```

Vous pouvez utiliser `StaticResource` pour récupérer une couleur à partir d’un dictionnaire de ressources :

```xaml
<StaticResource Key="myColor" />
```

Vers la fin de cet article, vous verrez une extension de balisage XAML personnalisée qui crée également une nouvelle valeur de couleur :

```xaml
<local:HslColor H="0.5" S="1.0" L="0.5" />
```

Quand vous définissez des tableaux de types communs tels que des chaînes ou des nombres, utilisez les balises listées dans l’article [**passage d’arguments de constructeur**](~/xamarin-forms/xaml/passing-arguments.md#constructor_arguments) pour délimiter les valeurs.

<a name="null" />

## <a name="xnull-markup-extension"></a>x:Null (extension de balisage)

L' `x:Null` extension de balisage est prise [`NullExtension`](xref:Xamarin.Forms.Xaml.NullExtension) en charge par la classe. Il n’a pas de propriétés et est simplement l’équivalent XAML du [`null`](/dotnet/csharp/language-reference/keywords/null/) mot clé C#.

L' `x:Null` extension de balisage est rarement nécessaire et rarement utilisée, mais si vous en avez besoin, vous êtes heureux de voir qu’elle existe.

La page de **démonstration de x :null** illustre un scénario `x:Null` dans lequel peut être pratique. Supposons que vous définissez un `Style` implicite pour `Label` , `Setter` qui comprend un `FontFamily` qui définit la propriété sur un nom de famille dépendant de la plateforme :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="MarkupExtensions.NullDemoPage"
             Title="x:Null Demo">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Label">
                <Setter Property="FontSize" Value="48" />
                <Setter Property="FontFamily">
                    <Setter.Value>
                        <OnPlatform x:TypeArguments="x:String">
                            <On Platform="iOS" Value="Times New Roman" />
                            <On Platform="Android" Value="serif" />
                            <On Platform="UWP" Value="Times New Roman" />
                        </OnPlatform>
                    </Setter.Value>
                </Setter>
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <ContentPage.Content>
        <StackLayout Padding="10, 0">
            <Label Text="Text 1" />
            <Label Text="Text 2" />

            <Label Text="Text 3"
                   FontFamily="{x:Null}" />

            <Label Text="Text 4" />
            <Label Text="Text 5" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>   
```

Ensuite, vous découvrez que pour l’un `Label` des éléments, vous souhaitez que tous les paramètres de propriété `Style` dans l’exception `FontFamily`implicite, à l’exception de, qui doit être la valeur par défaut. Vous pouvez en définir `Style` une autre à cet effet, mais une approche plus simple consiste simplement `FontFamily` à définir la propriété `Label` du `x:Null`particulier sur, comme illustré dans `Label`le centre.

Voici le programme en cours d’exécution :

[![Démonstration de x :Null](consuming-images/nulldemo-small.png "Démonstration de x :Null")](consuming-images/nulldemo-large.png#lightbox "Démonstration de x :Null")

Notez que quatre des `Label` éléments ont une police serif, mais que le centre `Label` possède la police sans empattement par défaut.

<a name="onplatform" />

## <a name="onplatform-markup-extension"></a>Extension de balisage OnPlatform

L’extension de balisage `OnPlatform` vous permet de personnaliser l’apparence de l’interface utilisateur pour chaque plateforme. Il fournit les mêmes fonctionnalités que les [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1) classes [`On`](xref:Xamarin.Forms.On) et, mais avec une représentation plus concise.

L' `OnPlatform` extension de balisage est prise [`OnPlatformExtension`](xref:Xamarin.Forms.Xaml.OnPlatformExtension) en charge par la classe, qui définit les propriétés suivantes :

- `Default`de type `object`, que vous affectez à une valeur par défaut à appliquer aux propriétés qui représentent des plateformes.
- `Android`de type `object`, que vous définissez sur une valeur à appliquer sur Android.
- `GTK`de type `object`, que vous définissez sur une valeur à appliquer sur les plateformes GTK.
- `iOS`de type `object`, que vous définissez sur une valeur à appliquer sur iOS.
- `macOS`de type `object`, que vous définissez sur une valeur à appliquer sur MacOS.
- `Tizen`de type `object`, que vous définissez sur une valeur à appliquer sur la plateforme Tizen.
- `UWP`de type `object`, que vous définissez sur une valeur à appliquer sur le plateforme Windows universelle.
- `WPF`de type `object`, que vous affectez à une valeur à appliquer sur la plateforme Windows Presentation Foundation.
- `Converter`de type `IValueConverter`, qui peut être défini sur une `IValueConverter` implémentation.
- `ConverterParameter`de type `object`, qui peut être défini sur une valeur à passer à l' `IValueConverter` implémentation.

> [!NOTE]
> L’analyseur XAML permet à [`OnPlatformExtension`](xref:Xamarin.Forms.Xaml.OnPlatformExtension) la classe d’être abrégée `OnPlatform`comme.

La `Default` propriété est la propriété de contenu `OnPlatformExtension`de. Par conséquent, pour les expressions de balisage XAML exprimées à l’aide d’accolades, vous pouvez éliminer la `Default=` partie de l’expression à condition qu’il s’agisse du premier argument. Si la `Default` propriété n’est pas définie, elle prend par [`BindableProperty.DefaultValue`](xref:Xamarin.Forms.BindableProperty.DefaultValue) défaut la valeur de la propriété, à condition que l' [`BindableProperty`](xref:Xamarin.Forms.BindableProperty)extension de balisage cible un.

> [!IMPORTANT]
> L’analyseur XAML s’attend à ce que les valeurs du type correct soient fournies aux propriétés utilisant l' `OnPlatform` extension de balisage. Si la conversion de type est nécessaire `OnPlatform` , l’extension de balisage tente de l’exécuter à l’aide des convertisseurs par défaut fournis par Xamarin. Forms. Toutefois, certaines conversions de type ne peuvent pas être effectuées par les convertisseurs par défaut et, dans ce `Converter` cas, la propriété doit être `IValueConverter` définie sur une implémentation.

La page de **démonstration OnPlatform** montre comment utiliser l' `OnPlatform` extension de balisage :

```xaml
<BoxView Color="{OnPlatform Yellow, iOS=Red, Android=Green, UWP=Blue}"
         WidthRequest="{OnPlatform 250, iOS=200, Android=300, UWP=400}"  
         HeightRequest="{OnPlatform 250, iOS=200, Android=300, UWP=400}"
         HorizontalOptions="Center" />
```

Dans cet exemple, les trois `OnPlatform` expressions utilisent la version abrégée du nom `OnPlatformExtension` de la classe. Les trois `OnPlatform` extensions de balisage [`Color`](xref:Xamarin.Forms.BoxView.Color)définissent [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) les propriétés, [`BoxView`](xref:Xamarin.Forms.BoxView) [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest)et du sur des valeurs différentes sur iOS, Android et UWP. Les extensions de balisage fournissent également des valeurs par défaut pour ces propriétés sur les plateformes qui ne `Default=` sont pas spécifiées, tout en éliminant la partie de l’expression. Notez que les propriétés d’extension de balisage définies sont séparées par des virgules.

Voici le programme en cours d’exécution :

[![Démonstration OnPlatform](consuming-images/onplatformdemo-small.png "Démonstration OnPlatform")](consuming-images/onplatformdemo-large.png#lightbox "Démonstration OnPlatform")

<a name="onidiom" />

## <a name="onidiom-markup-extension"></a>Extension de balisage OnIdiom

L' `OnIdiom` extension de balisage vous permet de personnaliser l’apparence de l’interface utilisateur en fonction de l’idiome du périphérique sur lequel l’application s’exécute. Elle est prise en charge [`OnIdiomExtension`](xref:Xamarin.Forms.Xaml.OnIdiomExtension) par la classe, qui définit les propriétés suivantes :

- `Default`de type `object`, que vous affectez à une valeur par défaut à appliquer aux propriétés qui représentent des idiomes de périphérique.
- `Phone`de type `object`, que vous définissez sur une valeur à appliquer sur les téléphones.
- `Tablet`de type `object`, que vous définissez sur une valeur à appliquer sur les tablettes.
- `Desktop`de type `object`, que vous définissez sur une valeur à appliquer sur les plateformes de bureau.
- `TV`de type `object`, que vous définissez sur une valeur à appliquer sur les plateformes TV.
- `Watch`de type `object`, que vous définissez sur une valeur à appliquer sur les plateformes de surveillance.
- `Converter`de type `IValueConverter`, qui peut être défini sur une `IValueConverter` implémentation.
- `ConverterParameter`de type `object`, qui peut être défini sur une valeur à passer à l' `IValueConverter` implémentation.

> [!NOTE]
> L’analyseur XAML permet à [`OnIdiomExtension`](xref:Xamarin.Forms.Xaml.OnIdiomExtension) la classe d’être abrégée `OnIdiom`comme.

La `Default` propriété est la propriété de contenu `OnIdiomExtension`de. Par conséquent, pour les expressions de balisage XAML exprimées à l’aide d’accolades, vous pouvez éliminer la `Default=` partie de l’expression à condition qu’il s’agisse du premier argument.

> [!IMPORTANT]
> L’analyseur XAML s’attend à ce que les valeurs du type correct soient fournies aux propriétés utilisant l' `OnIdiom` extension de balisage. Si la conversion de type est nécessaire `OnIdiom` , l’extension de balisage tente de l’exécuter à l’aide des convertisseurs par défaut fournis par Xamarin. Forms. Toutefois, certaines conversions de type ne peuvent pas être effectuées par les convertisseurs par défaut et, dans ce `Converter` cas, la propriété doit être `IValueConverter` définie sur une implémentation.

La page de **démonstration OnIdiom** montre comment utiliser l' `OnIdiom` extension de balisage :

```xaml
<BoxView Color="{OnIdiom Yellow, Phone=Red, Tablet=Green, Desktop=Blue}"
         WidthRequest="{OnIdiom 100, Phone=200, Tablet=300, Desktop=400}"
         HeightRequest="{OnIdiom 100, Phone=200, Tablet=300, Desktop=400}"
         HorizontalOptions="Center" />
```

Dans cet exemple, les trois `OnIdiom` expressions utilisent la version abrégée du nom `OnIdiomExtension` de la classe. Les trois `OnIdiom` extensions de balisage [`Color`](xref:Xamarin.Forms.BoxView.Color)définissent [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) les propriétés, [`BoxView`](xref:Xamarin.Forms.BoxView) [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest)et de la propriété sur des valeurs différentes sur les idiomes de téléphone, de tablette et de bureau. Les extensions de balisage fournissent également des valeurs par défaut pour ces propriétés sur les idiomes qui ne sont `Default=` pas spécifiés, tout en éliminant la partie de l’expression. Notez que les propriétés d’extension de balisage définies sont séparées par des virgules.

Voici le programme en cours d’exécution :

[![Démonstration OnIdiom](consuming-images/onidiomdemo-small.png "Démonstration OnIdiom")](consuming-images/onidiomdemo-large.png#lightbox "Démonstration OnIdiom")

## <a name="datatemplate-markup-extension"></a>DataTemplate (extension de balisage)

L' `DataTemplate` extension de balisage vous permet de convertir un type [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)en. Elle est prise en charge `DataTemplateExtension` par la classe, qui `TypeName` définit une propriété, `string`de type, qui est définie sur le nom du type à convertir en `DataTemplate`. La `TypeName` propriété est la propriété de contenu `DataTemplateExtension`de. Par conséquent, pour les expressions de balisage XAML exprimées à l’aide d’accolades, vous pouvez éliminer la `TypeName=` partie de l’expression.

> [!NOTE]
> L’analyseur XAML permet à `DataTemplateExtension` la classe d’être abrégée `DataTemplate`comme.

Une utilisation classique de cette extension de balisage se trouve dans une application d’environnement, comme illustré dans l’exemple suivant :

```xaml
<ShellContent Title="Monkeys"
              Icon="monkey.png"
              ContentTemplate="{DataTemplate views:MonkeysPage}" />
```

Dans cet exemple, `MonkeysPage` est converti d’un [`ContentPage`](xref:Xamarin.Forms.ContentPage) en [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), qui est défini comme valeur de la `ShellContent.ContentTemplate` propriété. Cela permet de `MonkeysPage` s’assurer que est uniquement créé lorsque la navigation vers la page se produit, plutôt qu’au démarrage de l’application.

Pour plus d’informations sur les applications de l’interpréteur de commandes, consultez [Xamarin. Forms Shell](~/xamarin-forms/app-fundamentals/shell/index.md).

## <a name="fontimage-markup-extension"></a>FontImage, extension de balisage

L' `FontImage` extension de balisage vous permet d’afficher une icône de police dans n’importe quelle `ImageSource`vue pouvant afficher un. Il fournit les mêmes fonctionnalités que la `FontImageSource` classe, mais avec une représentation plus concise.

L' `FontImage` extension de balisage est prise `FontImageExtension` en charge par la classe, qui définit les propriétés suivantes :

- `FontFamily`de type `string`, la famille de polices à laquelle appartient l’icône de police.
- `Glyph`de type `string`, la valeur de caractère Unicode de l’icône de police.
- `Color`de type [`Color`](xref:Xamarin.Forms.Color), la couleur à utiliser lors de l’affichage de l’icône de police.
- `Size`de type `double`, la taille, en unités indépendantes du périphérique, de l’icône de police rendue. La valeur par défaut est 30. En outre, cette propriété peut être définie sur une taille de police nommée.

> [!NOTE]
> L’analyseur XAML permet à `FontImageExtension` la classe d’être abrégée `FontImage`comme.

La `Glyph` propriété est la propriété de contenu `FontImageExtension`de. Par conséquent, pour les expressions de balisage XAML exprimées à l’aide d’accolades, vous pouvez éliminer la `Glyph=` partie de l’expression à condition qu’il s’agisse du premier argument.

La page de **démonstration FontImage** montre comment utiliser l' `FontImage` extension de balisage :

```xaml
<Image BackgroundColor="#D1D1D1"
       Source="{FontImage &#xf30c;, FontFamily={OnPlatform iOS=Ionicons, Android=ionicons.ttf#}, Size=44}" />
```

Dans cet exemple, la version abrégée du nom `FontImageExtension` de la classe est utilisée pour afficher une icône Xbox, à partir de la famille de polices [`Image`](xref:Xamarin.Forms.Image)Ionicons, dans un. L’expression utilise également l' `OnPlatform` extension de balisage pour `FontFamily` spécifier des valeurs de propriété différentes sur iOS et Android. En outre, la `Glyph=` partie de l’expression est éliminée, et les propriétés d’extension de balisage définies sont séparées par des virgules. Notez que, si le caractère Unicode de l’icône `\uf30c`est, il doit être placé dans une séquence d’échappement en `&#xf30c;`XAML et devient donc.

Voici le programme en cours d’exécution :

[![Capture d’écran de l’extension de balisage FontImage](consuming-images/fontimagedemo.png "Démonstration FontImage")](consuming-images/fontimagedemo-large.png#lightbox "Démonstration FontImage")

Pour plus d’informations sur l’affichage des icônes de police en spécifiant `FontImageSource` les données d’icône de police dans un objet, consultez [afficher les icônes de police](~/xamarin-forms/user-interface/text/fonts.md#display-font-icons).

## <a name="onapptheme-markup-extension"></a>Extension de balisage OnAppTheme

L' `OnAppTheme` extension de balisage vous permet de spécifier une ressource à consommer, telle qu’une image ou une couleur, en fonction du thème système actuel. Il fournit les mêmes fonctionnalités que la `OnAppTheme<T>` classe, mais avec une représentation plus concise.

> [!IMPORTANT]
> L' `OnAppTheme` extension de balisage a une configuration minimale requise pour le système d’exploitation. Pour plus d’informations, consultez [répondre aux modifications de thème du système dans les applications Xamarin. Forms](~/xamarin-forms/user-interface/theming/system-theme-changes.md).

L' `OnAppTheme` extension de balisage est prise `OnAppThemeExtension` en charge par la classe, qui définit les propriétés suivantes :

- `Default`, de type `object`, que vous définissez sur la ressource à utiliser par défaut.
- `Light`, de type `object`, que vous définissez sur la ressource à utiliser lorsque l’appareil utilise son thème clair.
- `Dark`, de type `object`, que vous définissez sur la ressource à utiliser lorsque l’appareil utilise son thème sombre.
- `Value`, de type `object`, qui retourne la ressource actuellement utilisée par l’extension de balisage.
- `Converter`de type `IValueConverter`, qui peut être défini sur une `IValueConverter` implémentation.
- `ConverterParameter`de type `object`, qui peut être défini sur une valeur à passer à l' `IValueConverter` implémentation.

> [!NOTE]
> L’analyseur XAML permet à `OnAppThemeExtension` la classe d’être abrégée `OnAppTheme`comme.

La `Default` propriété est la propriété de contenu `OnAppThemeExtension`de. Par conséquent, pour les expressions de balisage XAML exprimées à l’aide d’accolades, vous pouvez éliminer la `Default=` partie de l’expression à condition qu’il s’agisse du premier argument.

La page de **démonstration OnAppTheme** montre comment utiliser l' `OnAppTheme` extension de balisage :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="MarkupExtensions.OnAppThemeDemoPage"
             Title="OnAppTheme Demo">
    <ContentPage.Resources>

        <Style x:Key="labelStyle"
               TargetType="Label">
            <Setter Property="TextColor"
                    Value="{OnAppTheme Black, Light=Blue, Dark=Teal}" />
        </Style>

    </ContentPage.Resources>
    <StackLayout Margin="20">
        <Label Text="This text is green in light mode, and red in dark mode."
               TextColor="{OnAppTheme Light=Green, Dark=Red}" />
        <Label Text="This text is black by default, blue in light mode, and teal in dark mode."
               Style="{DynamicResource labelStyle}" />
    </StackLayout>
</ContentPage>
```

Dans cet exemple, la couleur de texte du premier [`Label`](xref:Xamarin.Forms.Label) est définie sur la couleur verte lorsque l’appareil utilise son thème clair, et est défini sur rouge lorsque l’appareil utilise son thème sombre. La deuxième `Label` a sa [`TextColor`](xref:Xamarin.Forms.Label.TextColor) propriété définie via un [`Style`](xref:Xamarin.Forms.Style). Cela `Style` définit la couleur de texte du `Label` à noir par défaut, le bleu lorsque l’appareil utilise son thème clair et la couleur bleu-vert lorsque l’appareil utilise son thème sombre.

> [!NOTE]
> Un [`Style`](xref:Xamarin.Forms.Style) qui utilise l' `OnAppTheme` extension de balisage doit être appliqué à un contrôle avec `DynamicResource` l’extension de balisage, afin que l’interface utilisateur de l’application soit mise à jour lorsque le thème système change.

Voici le programme en cours d’exécution :

![Démonstration OnAppTheme](consuming-images/onappthemedemo.png "Démonstration OnAppTheme")

## <a name="define-markup-extensions"></a>Définir des extensions de balisage

Si vous avez eu besoin d’une extension de balisage XAML qui n’est pas disponible dans Xamarin. Forms, vous pouvez [créer les vôtres](creating.md).

## <a name="related-links"></a>Liens connexes

- [Extensions de balisage (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-markupextensions)
- [Chapitre sur les extensions de balisage XAML du livre Xamarin. Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter10.md)
- [Dictionnaires de ressources](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Styles dynamiques](~/xamarin-forms/user-interface/styles/dynamic.md)
- [Liaison de données](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [Interpréteur de commandes Xamarin.Forms](~/xamarin-forms/app-fundamentals/shell/index.md)
- [Répondre aux modifications de thème du système dans les applications Xamarin. Forms](~/xamarin-forms/user-interface/theming/system-theme-changes.md)
