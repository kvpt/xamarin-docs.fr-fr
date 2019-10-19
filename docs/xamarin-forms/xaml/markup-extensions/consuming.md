---
title: Consommation des extensions de balisage XAML
description: Cet article explique comment utiliser les extensions de balisage XAML Xamarin. Forms pour améliorer la puissance et la flexibilité du code XAML en permettant de définir des attributs d’élément à partir de diverses sources.
ms.prod: xamarin
ms.assetid: CE686893-609C-4EC3-9225-6C68D2A9F79C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2019
ms.openlocfilehash: 03aaf471479a5113aade6bd3f34034afadfb538c
ms.sourcegitcommit: dad4dfcd194b63ec9e903363351b6d9e543d4888
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/18/2019
ms.locfileid: "69887900"
---
# <a name="consuming-xaml-markup-extensions"></a>Consommation des extensions de balisage XAML

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-markupextensions)

Les extensions de balisage XAML aident à améliorer la puissance et la flexibilité du XAML en permettant de définir des attributs d’élément à partir de diverses sources. Plusieurs extensions de balisage XAML font partie de la spécification XAML 2009. Ils s’affichent dans les fichiers XAML avec le préfixe d’espace de noms `x` personnalisé et sont communément désignés par ce préfixe. Cet article aborde les extensions de balisage suivantes :

- [`x:Static`](#static) : référencer des propriétés statiques, des champs ou des membres d’énumération.
- [`x:Reference`](#reference) : référencez des éléments nommés sur la page.
- [`x:Type`](#type) : définissez un attribut sur un objet `System.Type`.
- [`x:Array`](#array) : construisez un tableau d’objets d’un type particulier.
- [`x:Null`](#null) : définissez un attribut sur une valeur de `null`.
- [`OnPlatform`](#onplatform) : personnaliser l’apparence de l’interface utilisateur sur la base de chaque plateforme.
- [`OnIdiom`](#onidiom) : personnaliser l’apparence de l’interface utilisateur en fonction de l’idiome du périphérique sur lequel l’application s’exécute.
- [`DataTemplate`](#datatemplate-markup-extension) : convertit un type en [`DataTemplate`](xref:Xamarin.Forms.DataTemplate).
- [`FontImage`](#fontimage-markup-extension) : Affichez une icône de police dans n’importe quelle vue pouvant afficher une `ImageSource`.

Des extensions de balisage XAML supplémentaires ont été prises en charge par d’autres implémentations XAML, et sont également prises en charge par Xamarin. Forms. Celles-ci sont décrites plus en détail dans d’autres articles :

- `StaticResource` &ndash; des objets de référence à partir d’un dictionnaire de ressources, comme décrit dans l’article [**dictionnaires de ressources**](~/xamarin-forms/xaml/resource-dictionaries.md).
- `DynamicResource` &ndash; répondre aux modifications apportées aux objets dans un dictionnaire de ressources, comme décrit dans l’article [**styles dynamiques**](~/xamarin-forms/user-interface/styles/dynamic.md).
- `Binding` &ndash; établir un lien entre les propriétés de deux objets, comme décrit dans l’article [**liaison de données**](~/xamarin-forms/app-fundamentals/data-binding/index.md).
- `TemplateBinding` &ndash; effectue une liaison de données à partir d’un modèle de contrôle, comme indiqué dans l’article [**liaison à partir d’un modèle de contrôle**](~/xamarin-forms/app-fundamentals/templates/control-templates/template-binding.md).

La disposition [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) utilise l’extension de balisage personnalisée [`ConstraintExpression`](xref:Xamarin.Forms.ConstraintExpression). Cette extension de balisage est décrite dans l’article [**RelativeLayout**](~/xamarin-forms/user-interface/layouts/relative-layout.md).

<a name="static" />

## <a name="xstatic-markup-extension"></a>x:Static, extension de balisage

L’extension de balisage `x:Static` est prise en charge par la classe [`StaticExtension`](xref:Xamarin.Forms.Xaml.StaticExtension) . La classe a une propriété unique nommée [`Member`](xref:Xamarin.Forms.Xaml.StaticExtension.Member) de type `string` que vous définissez sur le nom d’une constante publique, d’une propriété statique, d’un champ statique ou d’un membre d’énumération.

Une façon courante d’utiliser `x:Static` consiste à définir d’abord une classe avec des constantes ou des variables statiques, telles que cette petite `AppConstants` classe dans le programme [**MarkupExtensions**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-markupextensions) :

```csharp
static class AppConstants
{
    public static double NormalFontSize = 18;
}
```

La page de **démonstration de x :static** montre plusieurs façons d’utiliser l’extension de balisage `x:Static`. L’approche la plus détaillée instancie la classe `StaticExtension` entre `Label.FontSize` balises d’éléments de propriété :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:sys="clr-namespace:System;assembly=mscorlib"
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

L’analyseur XAML permet également à la classe `StaticExtension` d’être abrégée comme `x:Static` :

```xaml
<Label Text="Label No. 2">
    <Label.FontSize>
        <x:Static Member="local:AppConstants.NormalFontSize" />
    </Label.FontSize>
</Label>
```

Cela peut encore être simplifié, mais la modification introduit une nouvelle syntaxe : elle consiste à placer la classe `StaticExtension` et le paramètre de membre entre accolades. L’expression résultante est définie directement sur l’attribut `FontSize` :

```xaml
<Label Text="Label No. 3"
       FontSize="{x:StaticExtension Member=local:AppConstants.NormalFontSize}" />
```

Notez qu’il n’y a *pas* de guillemets à l’intérieur des accolades. La propriété `Member` de `StaticExtension` n’est plus un attribut XML. Au lieu de cela, elle fait partie de l’expression de l’extension de balisage.

Tout comme vous pouvez abréger `x:StaticExtension` pour `x:Static` quand vous l’utilisez en tant qu’élément objet, vous pouvez également l’abréger dans l’expression entre accolades :

```xaml
<Label Text="Label No. 4"
       FontSize="{x:Static Member=local:AppConstants.NormalFontSize}" />
```

La classe `StaticExtension` a un attribut `ContentProperty` référençant la propriété `Member`, qui marque cette propriété comme la propriété de contenu par défaut de la classe. Pour les extensions de balisage XAML exprimées à l’aide d’accolades, vous pouvez éliminer la `Member=` partie de l’expression :

```xaml
<Label Text="Label No. 5"
       FontSize="{x:Static local:AppConstants.NormalFontSize}" />
```

Il s’agit de la forme la plus courante de l’extension de balisage `x:Static`.

La page de **démonstration statique** contient deux autres exemples. La balise racine du fichier XAML contient une déclaration d’espace de noms XML pour l’espace de noms .NET `System` :

```xaml
xmlns:sys="clr-namespace:System;assembly=mscorlib"
```

Cela permet de définir la taille de police `Label` sur le champ statique `Math.PI`. Cela donne lieu à un texte plutôt petit, donc la propriété `Scale` est définie sur `Math.E` :

```xaml
<Label Text="&#x03C0; &#x00D7; E sized text"
       FontSize="{x:Static sys:Math.PI}"
       Scale="{x:Static sys:Math.E}"
       HorizontalOptions="Center" />
```

Le dernier exemple affiche la valeur `Device.RuntimePlatform`. La propriété statique `Environment.NewLine` est utilisée pour insérer un caractère de nouvelle ligne entre les deux objets `Span` :

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

## <a name="xreference-markup-extension"></a>x:Référence, extension de balisage

L’extension de balisage `x:Reference` est prise en charge par la classe [`ReferenceExtension`](xref:Xamarin.Forms.Xaml.ReferenceExtension) . La classe a une propriété unique nommée [`Name`](xref:Xamarin.Forms.Xaml.ReferenceExtension.Name) de type `string` que vous définissez sur le nom d’un élément de la page qui a reçu un nom avec `x:Name`. Cette propriété de `Name` est la propriété de contenu de `ReferenceExtension`, donc `Name=` n’est pas nécessaire quand `x:Reference` apparaît entre accolades.

L’extension de balisage `x:Reference` est utilisée exclusivement avec les liaisons de données, qui sont décrites plus en détail dans l’article [**liaison de données**](~/xamarin-forms/app-fundamentals/data-binding/index.md).

La page de démonstration de la fonction **x :Reference** montre deux utilisations de `x:Reference` avec des liaisons de données, la première où elle est utilisée pour définir la propriété `Source` de l’objet `Binding`, et la seconde où elle est utilisée pour définir la propriété `BindingContext` pour deux liaisons de données :

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

Les deux expressions `x:Reference` utilisent la version abrégée du nom de la classe `ReferenceExtension` et éliminent la partie `Name=` de l’expression. Dans le premier exemple, l’extension de balisage `x:Reference` est incorporée dans l’extension de balisage `Binding`. Notez que les paramètres `Source` et `StringFormat` sont séparés par des virgules. Voici le programme en cours d’exécution :

[![Démonstration de la x :Reference](consuming-images/referencedemo-small.png "Démonstration de la x :Reference")](consuming-images/referencedemo-large.png#lightbox "Démonstration de la x :Reference")

<a name="type" />

## <a name="xtype-markup-extension"></a>x:Type, extension de balisage

L’extension de balisage `x:Type` est l’équivalent XAML C# du mot clé [`typeof`](/dotnet/csharp/language-reference/keywords/typeof/) . Il est pris en charge par la classe [`TypeExtension`](xref:Xamarin.Forms.Xaml.TypeExtension) , qui définit une propriété nommée [`TypeName`](xref:Xamarin.Forms.Xaml.TypeExtension.TypeName) de type `string` qui a pour valeur un nom de classe ou de structure. L’extension de balisage `x:Type` retourne l’objet [`System.Type`](xref:System.Type) de cette classe ou structure. `TypeName` est la propriété de contenu de `TypeExtension`, donc `TypeName=` n’est pas nécessaire lorsque `x:Type` apparaît avec des accolades.

Dans Xamarin. Forms, il existe plusieurs propriétés qui ont des arguments de type `Type`. Il peut s’agir, par exemple, de la propriété [`TargetType`](xref:Xamarin.Forms.Style.TargetType) de `Style` et de l’attribut [x :TypeArguments](~/xamarin-forms/xaml/passing-arguments.md#generic_type_arguments) utilisé pour spécifier des arguments dans des classes génériques. Toutefois, l’analyseur XAML effectue automatiquement l’opération de `typeof`, et l’extension de balisage `x:Type` n’est pas utilisée dans ces cas.

L’extension de balisage `x:Array`, qui est décrite dans la [section suivante](#array), est un emplacement où `x:Type` *est* requis.

L’extension de balisage `x:Type` est également utile lors de la construction d’un menu où chaque élément de menu correspond à un objet d’un type particulier. Vous pouvez associer un objet `Type` à chaque élément de menu, puis instancier l’objet quand l’élément de menu est sélectionné.

Voici comment fonctionne le menu de navigation dans `MainPage` dans le programme d' **extensions de balisage** . Le fichier **MainPage. Xaml** contient un `TableView` avec chaque `TextCell` correspondant à une page particulière dans le programme :

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

Chaque propriété `CommandParameter` est définie sur une extension de balisage `x:Type` qui fait référence à l’une des autres pages. La propriété `Command` est liée à une propriété nommée `NavigateCommand`. Cette propriété est définie dans le fichier code-behind `MainPage` :

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

La propriété `NavigateCommand` est un objet `Command` qui implémente une commande Execute avec un argument de type `Type` &mdash; la valeur de `CommandParameter`. La méthode utilise `Activator.CreateInstance` pour instancier la page, puis y accède. Le constructeur conclut en affectant à la `BindingContext` de la page la valeur elle-même, ce qui permet au `Binding` sur `Command` de fonctionner. Pour plus d’informations sur ce type de code, consultez l’article sur la [**liaison de données**](~/xamarin-forms/app-fundamentals/data-binding/index.md) et en particulier l’article sur les [**commandes**](~/xamarin-forms/app-fundamentals/data-binding/commanding.md) .

La page de **démonstration x :type** utilise une technique similaire pour instancier des éléments Xamarin. Forms et les ajouter à un `StackLayout`. Le fichier XAML se compose initialement de trois `Button` éléments avec leurs propriétés `Command` définies sur un `Binding` et les propriétés `CommandParameter` définies sur des types de trois vues Xamarin. Forms :

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

Le fichier code-behind définit et initialise la propriété `CreateCommand` :

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

La méthode qui est exécutée quand un `Button` est enfoncé crée une nouvelle instance de l’argument, définit sa propriété `VerticalOptions` et l’ajoute à la `StackLayout`. Les trois éléments `Button` partagent ensuite la page avec des vues créées dynamiquement :

[![Démonstration de x :Type](consuming-images/typedemo-small.png "Démonstration de x :Type")](consuming-images/typedemo-large.png#lightbox "Démonstration de x :Type")

<a name="array" />

## <a name="xarray-markup-extension"></a>x:Array, extension de balisage

L’extension de balisage `x:Array` vous permet de définir un tableau dans le balisage. Il est pris en charge par la classe [`ArrayExtension`](xref:Xamarin.Forms.Xaml.ArrayExtension) , qui définit deux propriétés :

- `Type` de type `Type`, qui indique le type des éléments dans le tableau.
- `Items` de type `IList`, qui est une collection des éléments eux-mêmes. Il s’agit de la propriété de contenu de `ArrayExtension`.

L’extension de balisage `x:Array` elle-même n’apparaît jamais entre accolades. Au lieu de cela, `x:Array` balises de début et de fin délimitent la liste des éléments. Affectez à la propriété `Type` une extension de balisage `x:Type`.

La page de **démonstration x :Array** montre comment utiliser `x:Array` pour ajouter des éléments à un `ListView` en affectant à la propriété `ItemsSource` la valeur d’un tableau :

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

L' `ViewCell` crée une `BoxView` simple pour chaque entrée de couleur :

[![Démo x :Array](consuming-images/arraydemo-small.png "Démo x :Array")](consuming-images/arraydemo-large.png#lightbox "Démo x :Array")

Il existe plusieurs façons de spécifier les éléments de `Color` individuels dans ce tableau. Vous pouvez utiliser une extension de balisage `x:Static` :

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

## <a name="xnull-markup-extension"></a>x:Null, extension de balisage

L’extension de balisage `x:Null` est prise en charge par la classe [`NullExtension`](xref:Xamarin.Forms.Xaml.NullExtension) . Il n’a pas de propriétés et est simplement l’équivalent XAML C# du mot clé [`null`](/dotnet/csharp/language-reference/keywords/null/) .

L’extension de balisage `x:Null` est rarement nécessaire et rarement utilisée, mais si vous en avez besoin, vous êtes heureux d’y parvenir.

La page de **démonstration de x :null** illustre un scénario dans lequel `x:Null` peut être pratique. Supposons que vous définissez un `Style` implicite pour `Label` qui comprend un `Setter` qui définit la propriété `FontFamily` sur un nom de famille dépendant de la plateforme :

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

Ensuite, vous découvrez que pour l’un des éléments de `Label`, vous souhaitez que tous les paramètres de propriété du `Style` implicite, à l’exception de la `FontFamily`, qui doit être la valeur par défaut. Vous pouvez définir un autre `Style` à cet effet, mais une approche plus simple consiste simplement à définir la `FontFamily` propriété du `Label` particulier à `x:Null`, comme illustré dans la `Label` centrale.

Voici le programme en cours d’exécution :

[![Démonstration de x :Null](consuming-images/nulldemo-small.png "Démonstration de x :Null")](consuming-images/nulldemo-large.png#lightbox "Démonstration de x :Null")

Notez que quatre des éléments `Label` ont une police serif, mais que le centre `Label` possède la police sans empattement par défaut.

<a name="onplatform" />

## <a name="onplatform-markup-extension"></a>Extension de balisage OnPlatform

L’extension de balisage `OnPlatform` vous permet de personnaliser l’apparence de l’interface utilisateur sur la base de chaque plateforme. Il fournit les mêmes fonctionnalités que les classes [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1) et [`On`](xref:Xamarin.Forms.On) , mais avec une représentation plus concise.

L’extension de balisage `OnPlatform` est prise en charge par la classe [`OnPlatformExtension`](xref:Xamarin.Forms.Xaml.OnPlatformExtension) , qui définit les propriétés suivantes :

- `Default` de type `object`, que vous affectez à une valeur par défaut à appliquer aux propriétés qui représentent des plateformes.
- `Android` de type `object`, que vous définissez sur une valeur à appliquer sur Android.
- `GTK` de type `object`, que vous définissez sur une valeur à appliquer sur les plateformes GTK.
- `iOS` de type `object`, que vous définissez sur une valeur à appliquer sur iOS.
- `macOS` de type `object`, que vous définissez sur une valeur à appliquer sur macOS.
- `Tizen` de type `object`, que vous définissez sur une valeur à appliquer sur la plateforme Tizen.
- `UWP` de type `object`, que vous définissez sur une valeur à appliquer sur le plateforme Windows universelle.
- `WPF` de type `object`, que vous affectez à une valeur à appliquer sur la plateforme Windows Presentation Foundation.
- `Converter` de type `IValueConverter`, que vous définissez sur une implémentation de `IValueConverter`.
- `ConverterParameter` de type `object`, que vous définissez sur une valeur à passer à l’implémentation de `IValueConverter`.

> [!NOTE]
> L’analyseur XAML permet à la classe [`OnPlatformExtension`](xref:Xamarin.Forms.Xaml.OnPlatformExtension) d’être abrégée en `OnPlatform`.

La propriété de `Default` est la propriété de contenu de `OnPlatformExtension`. Par conséquent, pour les expressions de balisage XAML exprimées à l’aide d’accolades, vous pouvez éliminer la `Default=` partie de l’expression à condition qu’il s’agisse du premier argument.

> [!IMPORTANT]
> L’analyseur XAML s’attend à ce que les valeurs du type correct soient fournies aux propriétés utilisant l’extension de balisage `OnPlatform`. Si la conversion de type est nécessaire, l’extension de balisage `OnPlatform` tente de l’exécuter à l’aide des convertisseurs par défaut fournis par Xamarin. Forms. Toutefois, certaines conversions de type ne peuvent pas être effectuées par les convertisseurs par défaut et, dans ce cas, la propriété `Converter` doit être définie sur une implémentation de `IValueConverter`.

La page de **démonstration OnPlatform** montre comment utiliser l’extension de balisage `OnPlatform` :

```xaml
<BoxView Color="{OnPlatform Yellow, iOS=Red, Android=Green, UWP=Blue}"
         WidthRequest="{OnPlatform 250, iOS=200, Android=300, UWP=400}"  
         HeightRequest="{OnPlatform 250, iOS=200, Android=300, UWP=400}"
         HorizontalOptions="Center" />
```

Dans cet exemple, les trois expressions `OnPlatform` utilisent la version abrégée du nom de la classe `OnPlatformExtension`. Les trois `OnPlatform` extensions de balisage définissent les propriétés [`Color`](xref:Xamarin.Forms.BoxView.Color), [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest)et [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) de la [`BoxView`](xref:Xamarin.Forms.BoxView) sur des valeurs différentes sur iOS, Android et UWP. Les extensions de balisage fournissent également des valeurs par défaut pour ces propriétés sur les plateformes qui ne sont pas spécifiées, tout en éliminant la `Default=` partie de l’expression. Notez que les propriétés d’extension de balisage définies sont séparées par des virgules.

Voici le programme en cours d’exécution :

[![Démonstration OnPlatform](consuming-images/onplatformdemo-small.png "Démonstration OnPlatform")](consuming-images/onplatformdemo-large.png#lightbox "Démonstration OnPlatform")

<a name="onidiom" />

## <a name="onidiom-markup-extension"></a>Extension de balisage OnIdiom

L’extension de balisage `OnIdiom` vous permet de personnaliser l’apparence de l’interface utilisateur en fonction de l’idiome du périphérique sur lequel l’application s’exécute. Elle est prise en charge par la classe [`OnIdiomExtension`](xref:Xamarin.Forms.Xaml.OnIdiomExtension) , qui définit les propriétés suivantes :

- `Default` de type `object`, que vous affectez à une valeur par défaut à appliquer aux propriétés qui représentent des idiomes de périphérique.
- `Phone` de type `object`, que vous définissez sur une valeur à appliquer sur les téléphones.
- `Tablet` de type `object`, que vous définissez sur une valeur à appliquer sur les tablettes.
- `Desktop` de type `object`, que vous définissez sur une valeur à appliquer sur les plateformes de bureau.
- `TV` de type `object`, que vous définissez sur une valeur à appliquer sur les plateformes TV.
- `Watch` de type `object`, que vous définissez sur une valeur à appliquer sur les plateformes de surveillance.
- `Converter` de type `IValueConverter`, que vous définissez sur une implémentation de `IValueConverter`.
- `ConverterParameter` de type `object`, que vous définissez sur une valeur à passer à l’implémentation de `IValueConverter`.

> [!NOTE]
> L’analyseur XAML permet à la classe [`OnIdiomExtension`](xref:Xamarin.Forms.Xaml.OnIdiomExtension) d’être abrégée en `OnIdiom`.

La propriété de `Default` est la propriété de contenu de `OnIdiomExtension`. Par conséquent, pour les expressions de balisage XAML exprimées à l’aide d’accolades, vous pouvez éliminer la `Default=` partie de l’expression à condition qu’il s’agisse du premier argument.

> [!IMPORTANT]
> L’analyseur XAML s’attend à ce que les valeurs du type correct soient fournies aux propriétés utilisant l’extension de balisage `OnIdiom`. Si la conversion de type est nécessaire, l’extension de balisage `OnIdiom` tente de l’exécuter à l’aide des convertisseurs par défaut fournis par Xamarin. Forms. Toutefois, certaines conversions de type ne peuvent pas être effectuées par les convertisseurs par défaut et, dans ce cas, la propriété `Converter` doit être définie sur une implémentation de `IValueConverter`.

La page de **démonstration OnIdiom** montre comment utiliser l’extension de balisage `OnIdiom` :

```xaml
<BoxView Color="{OnIdiom Yellow, Phone=Red, Tablet=Green, Desktop=Blue}"
         WidthRequest="{OnIdiom 100, Phone=200, Tablet=300, Desktop=400}"
         HeightRequest="{OnIdiom 100, Phone=200, Tablet=300, Desktop=400}"
         HorizontalOptions="Center" />
```

Dans cet exemple, les trois expressions `OnIdiom` utilisent la version abrégée du nom de la classe `OnIdiomExtension`. Les trois `OnIdiom` extensions de balisage définissent les propriétés de [`Color`](xref:Xamarin.Forms.BoxView.Color), [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest)et [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) de la [`BoxView`](xref:Xamarin.Forms.BoxView) sur des valeurs différentes sur les idiomes de téléphone, de tablette et de bureau. Les extensions de balisage fournissent également des valeurs par défaut pour ces propriétés sur les idiomes qui ne sont pas spécifiés, tout en éliminant la `Default=` partie de l’expression. Notez que les propriétés d’extension de balisage définies sont séparées par des virgules.

Voici le programme en cours d’exécution :

[![Démonstration OnIdiom](consuming-images/onidiomdemo-small.png "Démonstration OnIdiom")](consuming-images/onidiomdemo-large.png#lightbox "Démonstration OnIdiom")

## <a name="datatemplate-markup-extension"></a>DataTemplate (extension de balisage)

L’extension de balisage `DataTemplate` vous permet de convertir un type en [`DataTemplate`](xref:Xamarin.Forms.DataTemplate). Elle est prise en charge par la classe `DataTemplateExtension`, qui définit une propriété `TypeName`, de type `string`, qui est définie sur le nom du type à convertir en `DataTemplate`. La propriété de `TypeName` est la propriété de contenu de `DataTemplateExtension`. Par conséquent, pour les expressions de balisage XAML exprimées à l’aide d’accolades, vous pouvez éliminer la `TypeName=` partie de l’expression.

> [!NOTE]
> L’analyseur XAML permet à la classe `DataTemplateExtension` d’être abrégée en `DataTemplate`.

Une utilisation classique de cette extension de balisage se trouve dans une application d’environnement, comme illustré dans l’exemple suivant :

```xaml
<ShellContent Title="Monkeys"
              Icon="monkey.png"
              ContentTemplate="{DataTemplate views:MonkeysPage}" />
```

Dans cet exemple, `MonkeysPage` est converti à partir d’un [`ContentPage`](xref:Xamarin.Forms.ContentPage) en [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), qui est défini comme valeur de la propriété `ShellContent.ContentTemplate`. Cela permet de s’assurer que `MonkeysPage` est créé uniquement lorsque la navigation vers la page se produit, plutôt qu’au démarrage de l’application.

Pour plus d’informations sur les applications de l’interpréteur de commandes, consultez [Xamarin. Forms Shell](~/xamarin-forms/app-fundamentals/shell/index.md).

## <a name="fontimage-markup-extension"></a>Extension de balisage FontImage

L’extension de balisage `FontImage` vous permet d’afficher une icône de police dans n’importe quelle vue pouvant afficher une `ImageSource`. Il fournit les mêmes fonctionnalités que la classe `FontImageSource`, mais avec une représentation plus concise.

L’extension de balisage `FontImage` est prise en charge par la classe `FontImageExtension`, qui définit les propriétés suivantes :

- `FontFamily` de type `string`, famille de polices à laquelle appartient l’icône de police.
- `Glyph` de type `string`, la valeur du caractère Unicode de l’icône de police.
- `Color` de type `Color`, couleur à utiliser lors de l’affichage de l’icône de police.
- `Size` de type `double`, taille, en unités indépendantes du périphérique, de l’icône de police rendue.

> [!NOTE]
> L’analyseur XAML permet à la classe `FontImageExtension` d’être abrégée en `FontImage`.

La propriété de `Glyph` est la propriété de contenu de `FontImageExtension`. Par conséquent, pour les expressions de balisage XAML exprimées à l’aide d’accolades, vous pouvez éliminer la `Glyph=` partie de l’expression à condition qu’il s’agisse du premier argument.

La page de **démonstration FontImage** montre comment utiliser l’extension de balisage `FontImage` :

```xaml
<Image BackgroundColor="#D1D1D1"
       Source="{FontImage &#xf30c;, FontFamily={OnPlatform iOS=Ionicons, Android=ionicons.ttf#}, Size=44}" />
```

Dans cet exemple, la version abrégée du nom de la classe `FontImageExtension` est utilisée pour afficher une icône XBox, à partir de la famille de polices Ionicons, dans une [`Image`](xref:Xamarin.Forms.Image). L’expression utilise également l’extension de balisage `OnPlatform` pour spécifier différentes valeurs de propriété `FontFamily` sur iOS et Android. En outre, la `Glyph=` partie de l’expression est éliminée, et les propriétés d’extension de balisage définies sont séparées par des virgules. Notez que même si le caractère Unicode de l’icône est `\uf30c`, il doit être placé dans une séquence d’échappement en XAML et devient donc `&#xf30c;`.

Voici le programme en cours d’exécution :

[![Capture d’écran de l’extension de balisage FontImage](consuming-images/fontimagedemo.png "Démonstration FontImage")](consuming-images/fontimagedemo-large.png#lightbox "Démonstration FontImage")

Pour plus d’informations sur l’affichage des icônes de police en spécifiant les données d’icône de police dans un objet `FontImageSource`, consultez [afficher les icônes de police](~/xamarin-forms/user-interface/text/fonts.md#display-font-icons).

## <a name="define-your-own-markup-extensions"></a>Définir vos propres extensions de balisage

Si vous avez eu besoin d’une extension de balisage XAML qui n’est pas disponible dans Xamarin. Forms, vous pouvez [créer les vôtres](creating.md).

## <a name="related-links"></a>Liens associés

- [Extensions de balisage (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-markupextensions)
- [Chapitre sur les extensions de balisage XAML du livre Xamarin. Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter10.md)
- [Dictionnaires de ressources](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Styles dynamiques](~/xamarin-forms/user-interface/styles/dynamic.md)
- [Liaison de données](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [Xamarin. Forms Shell](~/xamarin-forms/app-fundamentals/shell/index.md).
