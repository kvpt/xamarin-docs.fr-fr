---
title: Consommation des extensions de balisage XAML
description: Cet article explique comment utiliser Xamarin.Forms les extensions de balisage XAML pour améliorer la puissance et la flexibilité du XAML en permettant de définir des attributs d’élément à partir de diverses sources.
ms.prod: xamarin
ms.assetid: CE686893-609C-4EC3-9225-6C68D2A9F79C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/17/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e1429c3f39e37dc552d7f6ca8767058e5aec853b
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84903105"
---
# <a name="consuming-xaml-markup-extensions"></a>Consommation des extensions de balisage XAML

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-markupextensions)

Les extensions de balisage XAML aident à améliorer la puissance et la flexibilité du XAML en permettant de définir des attributs d’élément à partir de diverses sources. Plusieurs extensions de balisage XAML font partie de la spécification XAML 2009. Ils s’affichent dans les fichiers XAML avec le `x` préfixe d’espace de noms personnalisé, et sont communément désignés par ce préfixe. Cet article aborde les extensions de balisage suivantes :

- [`x:Static`](#xstatic-markup-extension): référencez des propriétés statiques, des champs ou des membres d’énumération.
- [`x:Reference`](#xreference-markup-extension): référence des éléments nommés sur la page.
- [`x:Type`](#xtype-markup-extension): définissez un attribut sur un `System.Type` objet.
- [`x:Array`](#xarray-markup-extension): construisez un tableau d’objets d’un type particulier.
- [`x:Null`](#xnull-markup-extension): affectez une valeur à un attribut `null` .
- [`OnPlatform`](#onplatform-markup-extension): personnaliser l’apparence de l’interface utilisateur sur la base de chaque plateforme.
- [`OnIdiom`](#onidiom-markup-extension): personnalisez l’apparence de l’interface utilisateur en fonction de l’idiome du périphérique sur lequel l’application s’exécute.
- [`DataTemplate`](#datatemplate-markup-extension): convertit un type en [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) .
- [`FontImage`](#fontimage-markup-extension): affiche une icône de police dans n’importe quelle vue pouvant afficher un `ImageSource` .
- [`AppThemeBinding`](#appthemebinding-markup-extension): utilise une ressource basée sur le thème système actuel.

Des extensions de balisage XAML supplémentaires ont été prises en charge par d’autres implémentations XAML, et sont également prises en charge par Xamarin.Forms . Celles-ci sont décrites plus en détail dans d’autres articles :

- `StaticResource`-référencez des objets à partir d’un dictionnaire de ressources, comme décrit dans l’article [**dictionnaires de ressources**](~/xamarin-forms/xaml/resource-dictionaries.md).
- `DynamicResource`-répondre aux modifications apportées aux objets dans un dictionnaire de ressources, comme décrit dans l’article [**styles dynamiques**](~/xamarin-forms/user-interface/styles/dynamic.md).
- `Binding`: établissez un lien entre les propriétés de deux objets, comme décrit dans l’article [**liaison de données**](~/xamarin-forms/app-fundamentals/data-binding/index.md).
- `TemplateBinding`-effectue une liaison de données à partir d’un modèle de contrôle, comme indiqué dans l’article [** Xamarin.Forms modèles de contrôle**](~/xamarin-forms/app-fundamentals/templates/control-template.md).
- `RelativeSource`-définit la source de liaison par rapport à la position de la cible de liaison, comme indiqué dans l’article [liaisons relatives](~/xamarin-forms/app-fundamentals/data-binding/relative-bindings.md).

La [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) disposition utilise l’extension de balisage personnalisée [`ConstraintExpression`](xref:Xamarin.Forms.ConstraintExpression) . Cette extension de balisage est décrite dans l’article [**RelativeLayout**](~/xamarin-forms/user-interface/layouts/relative-layout.md).

## <a name="xstatic-markup-extension"></a>x:Static (extension de balisage)

L' `x:Static` extension de balisage est prise en charge par la [`StaticExtension`](xref:Xamarin.Forms.Xaml.StaticExtension) classe. La classe a une propriété unique nommée [`Member`](xref:Xamarin.Forms.Xaml.StaticExtension.Member) de type `string` que vous définissez sur le nom d’une constante publique, d’une propriété statique, d’un champ statique ou d’un membre d’énumération.

Une façon courante d’utiliser `x:Static` est de commencer par définir une classe avec des constantes ou des variables statiques, telles que cette petite `AppConstants` classe dans le programme [**MarkupExtensions**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-markupextensions) :

```csharp
static class AppConstants
{
    public static double NormalFontSize = 18;
}
```

La page de **démonstration de x :static** montre plusieurs façons d’utiliser l' `x:Static` extension de balisage. L’approche la plus détaillée instancie la `StaticExtension` classe entre les `Label.FontSize` balises d’éléments de propriété :

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

L’analyseur XAML permet également `StaticExtension` à la classe d’être abrégée comme `x:Static` suit :

```xaml
<Label Text="Label No. 2">
    <Label.FontSize>
        <x:Static Member="local:AppConstants.NormalFontSize" />
    </Label.FontSize>
</Label>
```

Cela peut être simplifié encore plus, mais la modification introduit une nouvelle syntaxe : elle consiste à placer la `StaticExtension` classe et le paramètre de membre entre accolades. L’expression résultante est définie directement sur l' `FontSize` attribut :

```xaml
<Label Text="Label No. 3"
       FontSize="{x:StaticExtension Member=local:AppConstants.NormalFontSize}" />
```

Notez qu’il n’y a *pas* de guillemets à l’intérieur des accolades. La `Member` propriété de `StaticExtension` n’est plus un attribut XML. Au lieu de cela, elle fait partie de l’expression de l’extension de balisage.

Tout comme vous pouvez l’abréger `x:StaticExtension` `x:Static` quand vous l’utilisez en tant qu’élément objet, vous pouvez également l’abréger dans l’expression entre accolades :

```xaml
<Label Text="Label No. 4"
       FontSize="{x:Static Member=local:AppConstants.NormalFontSize}" />
```

La `StaticExtension` classe a un `ContentProperty` attribut qui référence la propriété `Member` , qui marque cette propriété comme la propriété de contenu par défaut de la classe. Pour les extensions de balisage XAML exprimées à l’aide d’accolades, vous pouvez éliminer la `Member=` partie de l’expression :

```xaml
<Label Text="Label No. 5"
       FontSize="{x:Static local:AppConstants.NormalFontSize}" />
```

Il s’agit de la forme la plus courante de l' `x:Static` extension de balisage.

La page de **démonstration statique** contient deux autres exemples. La balise racine du fichier XAML contient une déclaration d’espace de noms XML pour l' `System` espace de noms .net :

```xaml
xmlns:sys="clr-namespace:System;assembly=netstandard"
```

Cela permet de `Label` définir la taille de police sur le champ statique `Math.PI` . Cela donne lieu à du texte plutôt petit, donc la `Scale` propriété est définie sur `Math.E` :

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

## <a name="xreference-markup-extension"></a>x :Reference (extension de balisage)

L' `x:Reference` extension de balisage est prise en charge par la [`ReferenceExtension`](xref:Xamarin.Forms.Xaml.ReferenceExtension) classe. La classe a une propriété unique nommée [`Name`](xref:Xamarin.Forms.Xaml.ReferenceExtension.Name) de type `string` que vous définissez sur le nom d’un élément de la page auquel un nom a été attribué `x:Name` . Cette `Name` propriété est la propriété de contenu de `ReferenceExtension` , ce qui `Name=` n’est pas obligatoire lorsque `x:Reference` s’affiche entre accolades.

L' `x:Reference` extension de balisage est utilisée exclusivement avec les liaisons de données, qui sont décrites plus en détail dans l’article [**liaison de données**](~/xamarin-forms/app-fundamentals/data-binding/index.md).

La page de démonstration de la fonction **x :Reference** montre deux utilisations de `x:Reference` avec les liaisons de données, la première où elle est utilisée pour définir la `Source` propriété de l' `Binding` objet, et la seconde où elle est utilisée pour définir la `BindingContext` propriété pour deux liaisons de données :

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

Les deux `x:Reference` expressions utilisent la version abrégée du `ReferenceExtension` nom de la classe et éliminent la `Name=` partie de l’expression. Dans le premier exemple, l' `x:Reference` extension de balisage est incorporée dans l' `Binding` extension de balisage. Notez que les `Source` `StringFormat` paramètres et sont séparés par des virgules. Voici le programme en cours d’exécution :

[![Démonstration de la x :Reference](consuming-images/referencedemo-small.png "Démonstration de la x :Reference")](consuming-images/referencedemo-large.png#lightbox "Démonstration de la x :Reference")

## <a name="xtype-markup-extension"></a>x:Type (extension de balisage)

L' `x:Type` extension de balisage est l’équivalent XAML du [`typeof`](/dotnet/csharp/language-reference/keywords/typeof/) mot clé C#. Elle est prise en charge par la [`TypeExtension`](xref:Xamarin.Forms.Xaml.TypeExtension) classe, qui définit une propriété nommée [`TypeName`](xref:Xamarin.Forms.Xaml.TypeExtension.TypeName) de type `string` qui a pour valeur un nom de classe ou de structure. L' `x:Type` extension de balisage retourne l' [`System.Type`](xref:System.Type) objet de cette classe ou structure. `TypeName`est la propriété de contenu de `TypeExtension` , ce qui `TypeName=` n’est pas obligatoire lorsque `x:Type` s’affiche entre accolades.

Dans Xamarin.Forms , il existe plusieurs propriétés qui ont des arguments de type `Type` . Il peut s’agir, par exemple [`TargetType`](xref:Xamarin.Forms.Style.TargetType) , de la propriété de `Style` et de l’attribut [x :TypeArguments](~/xamarin-forms/xaml/passing-arguments.md#specifying-a-generic-type-argument) utilisé pour spécifier des arguments dans des classes génériques. Toutefois, l’analyseur XAML effectue l' `typeof` opération automatiquement et l' `x:Type` extension de balisage n’est pas utilisée dans ces cas.

L’extension de `x:Type` *is* `x:Array` balisage, qui est décrite dans la [section suivante](#xarray-markup-extension), est un emplacement requis.

L' `x:Type` extension de balisage est également utile lors de la construction d’un menu où chaque élément de menu correspond à un objet d’un type particulier. Vous pouvez associer un `Type` objet à chaque élément de menu, puis instancier l’objet quand l’élément de menu est sélectionné.

Voici comment fonctionne le menu de navigation dans `MainPage` dans le programme des **extensions de balisage** . Le fichier **MainPage. Xaml** contient un `TableView` avec chaque `TextCell` correspondant à une page particulière dans le programme :

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

Chaque `CommandParameter` propriété est définie sur une `x:Type` extension de balisage qui fait référence à l’une des autres pages. La `Command` propriété est liée à une propriété nommée `NavigateCommand` . Cette propriété est définie dans le `MainPage` fichier code-behind :

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

La `NavigateCommand` propriété est un `Command` objet qui implémente une commande Execute avec un argument de type `Type` &mdash; la valeur `CommandParameter` . La méthode utilise `Activator.CreateInstance` pour instancier la page, puis y accède. Le constructeur conclut en affectant à la valeur `BindingContext` de la page elle-même, ce qui permet au de `Binding` `Command` fonctionner. Pour plus d’informations sur ce type de code, consultez l’article sur la [**liaison de données**](~/xamarin-forms/app-fundamentals/data-binding/index.md) et en particulier l’article sur les [**commandes**](~/xamarin-forms/app-fundamentals/data-binding/commanding.md) .

La page de **démonstration x :type** utilise une technique similaire pour instancier Xamarin.Forms des éléments et les ajouter à un `StackLayout` . Le fichier XAML se compose initialement de trois `Button` éléments dont les `Command` propriétés sont définies sur a `Binding` et les `CommandParameter` propriétés définies sur des types de trois Xamarin.Forms vues :

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

La méthode qui est exécutée quand un `Button` est enfoncé crée une nouvelle instance de l’argument, définit sa `VerticalOptions` propriété et l’ajoute à `StackLayout` . Les trois `Button` éléments partagent ensuite la page avec des vues créées dynamiquement :

[![Démonstration de x :Type](consuming-images/typedemo-small.png "Démonstration de x :Type")](consuming-images/typedemo-large.png#lightbox "Démonstration de x :Type")

## <a name="xarray-markup-extension"></a>x:Array (extension de balisage)

L' `x:Array` extension de balisage vous permet de définir un tableau dans le balisage. Elle est prise en charge par la [`ArrayExtension`](xref:Xamarin.Forms.Xaml.ArrayExtension) classe, qui définit deux propriétés :

- `Type`de type `Type` , qui indique le type des éléments dans le tableau.
- `Items`de type `IList` , qui est une collection d’éléments eux-mêmes. Il s’agit de la propriété de contenu de `ArrayExtension` .

L' `x:Array` extension de balisage elle-même n’apparaît jamais entre accolades. Au lieu de cela, `x:Array` les balises de début et de fin délimitent la liste des éléments. Affectez `Type` à la propriété une `x:Type` extension de balisage.

La page de **démonstration x :Array** montre comment utiliser `x:Array` pour ajouter des éléments à un `ListView` en affectant à la `ItemsSource` propriété un tableau :

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

Il existe plusieurs façons de spécifier les `Color` éléments individuels dans ce tableau. Vous pouvez utiliser une `x:Static` extension de balisage :

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

Quand vous définissez des tableaux de types communs tels que des chaînes ou des nombres, utilisez les balises listées dans l’article [**passage d’arguments de constructeur**](~/xamarin-forms/xaml/passing-arguments.md#passing-constructor-arguments) pour délimiter les valeurs.

## <a name="xnull-markup-extension"></a>x:Null (extension de balisage)

L' `x:Null` extension de balisage est prise en charge par la [`NullExtension`](xref:Xamarin.Forms.Xaml.NullExtension) classe. Il n’a pas de propriétés et est simplement l’équivalent XAML du [`null`](/dotnet/csharp/language-reference/keywords/null/) mot clé C#.

L' `x:Null` extension de balisage est rarement nécessaire et rarement utilisée, mais si vous en avez besoin, vous êtes heureux de voir qu’elle existe.

La page de **démonstration de x :null** illustre un scénario dans lequel `x:Null` peut être pratique. Supposons que vous définissez un implicite `Style` pour `Label` , qui comprend un `Setter` qui définit la `FontFamily` propriété sur un nom de famille dépendant de la plateforme :

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

Ensuite, vous découvrez que pour l’un des `Label` éléments, vous souhaitez que tous les paramètres de propriété dans l’exception implicite, `Style` à l’exception de `FontFamily` , qui doit être la valeur par défaut. Vous pouvez en définir une autre à `Style` cet effet, mais une approche plus simple consiste simplement à définir la `FontFamily` propriété du particulier `Label` sur `x:Null` , comme illustré dans le centre `Label` .

Voici le programme en cours d’exécution :

[![Démonstration de x :Null](consuming-images/nulldemo-small.png "Démonstration de x :Null")](consuming-images/nulldemo-large.png#lightbox "Démonstration de x :Null")

Notez que quatre des `Label` éléments ont une police serif, mais que le centre `Label` possède la police sans empattement par défaut.

## <a name="onplatform-markup-extension"></a>Extension de balisage OnPlatform

L’extension de balisage `OnPlatform` vous permet de personnaliser l’apparence de l’interface utilisateur pour chaque plateforme. Il fournit les mêmes fonctionnalités que les [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1) [`On`](xref:Xamarin.Forms.On) classes et, mais avec une représentation plus concise.

L' `OnPlatform` extension de balisage est prise en charge par la [`OnPlatformExtension`](xref:Xamarin.Forms.Xaml.OnPlatformExtension) classe, qui définit les propriétés suivantes :

- `Default`de type `object` , que vous affectez à une valeur par défaut à appliquer aux propriétés qui représentent des plateformes.
- `Android`de type `object` , que vous définissez sur une valeur à appliquer sur Android.
- `GTK`de type `object` , que vous définissez sur une valeur à appliquer sur les plateformes GTK.
- `iOS`de type `object` , que vous définissez sur une valeur à appliquer sur iOS.
- `macOS`de type `object` , que vous définissez sur une valeur à appliquer sur MacOS.
- `Tizen`de type `object` , que vous définissez sur une valeur à appliquer sur la plateforme Tizen.
- `UWP`de type `object` , que vous définissez sur une valeur à appliquer sur le plateforme Windows universelle.
- `WPF`de type `object` , que vous affectez à une valeur à appliquer sur la plateforme Windows Presentation Foundation.
- `Converter`de type `IValueConverter` , qui peut être défini sur une `IValueConverter` implémentation.
- `ConverterParameter`de type `object` , qui peut être défini sur une valeur à passer à l' `IValueConverter` implémentation.

> [!NOTE]
> L’analyseur XAML permet [`OnPlatformExtension`](xref:Xamarin.Forms.Xaml.OnPlatformExtension) à la classe d’être abrégée comme `OnPlatform` .

La `Default` propriété est la propriété de contenu de `OnPlatformExtension` . Par conséquent, pour les expressions de balisage XAML exprimées à l’aide d’accolades, vous pouvez éliminer la `Default=` partie de l’expression à condition qu’il s’agisse du premier argument. Si la `Default` propriété n’est pas définie, elle prend par défaut la valeur de la [`BindableProperty.DefaultValue`](xref:Xamarin.Forms.BindableProperty.DefaultValue) propriété, à condition que l’extension de balisage cible un [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) .

> [!IMPORTANT]
> L’analyseur XAML s’attend à ce que les valeurs du type correct soient fournies aux propriétés utilisant l' `OnPlatform` extension de balisage. Si la conversion de type est nécessaire, l' `OnPlatform` extension de balisage tente de l’exécuter à l’aide des convertisseurs par défaut fournis par Xamarin.Forms . Toutefois, certaines conversions de type ne peuvent pas être effectuées par les convertisseurs par défaut et, dans ce cas, la `Converter` propriété doit être définie sur une `IValueConverter` implémentation.

La page de **démonstration OnPlatform** montre comment utiliser l' `OnPlatform` extension de balisage :

```xaml
<BoxView Color="{OnPlatform Yellow, iOS=Red, Android=Green, UWP=Blue}"
         WidthRequest="{OnPlatform 250, iOS=200, Android=300, UWP=400}"  
         HeightRequest="{OnPlatform 250, iOS=200, Android=300, UWP=400}"
         HorizontalOptions="Center" />
```

Dans cet exemple, les trois `OnPlatform` expressions utilisent la version abrégée du nom de la `OnPlatformExtension` classe. Les trois `OnPlatform` extensions de balisage définissent les [`Color`](xref:Xamarin.Forms.BoxView.Color) [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) Propriétés, et [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) du [`BoxView`](xref:Xamarin.Forms.BoxView) sur des valeurs différentes sur iOS, Android et UWP. Les extensions de balisage fournissent également des valeurs par défaut pour ces propriétés sur les plateformes qui ne sont pas spécifiées, tout en éliminant la `Default=` partie de l’expression. Notez que les propriétés d’extension de balisage définies sont séparées par des virgules.

Voici le programme en cours d’exécution :

[![Démonstration OnPlatform](consuming-images/onplatformdemo-small.png "Démonstration OnPlatform")](consuming-images/onplatformdemo-large.png#lightbox "Démonstration OnPlatform")

## <a name="onidiom-markup-extension"></a>Extension de balisage OnIdiom

L' `OnIdiom` extension de balisage vous permet de personnaliser l’apparence de l’interface utilisateur en fonction de l’idiome du périphérique sur lequel l’application s’exécute. Elle est prise en charge par la [`OnIdiomExtension`](xref:Xamarin.Forms.Xaml.OnIdiomExtension) classe, qui définit les propriétés suivantes :

- `Default`de type `object` , que vous affectez à une valeur par défaut à appliquer aux propriétés qui représentent des idiomes de périphérique.
- `Phone`de type `object` , que vous définissez sur une valeur à appliquer sur les téléphones.
- `Tablet`de type `object` , que vous définissez sur une valeur à appliquer sur les tablettes.
- `Desktop`de type `object` , que vous définissez sur une valeur à appliquer sur les plateformes de bureau.
- `TV`de type `object` , que vous définissez sur une valeur à appliquer sur les plateformes TV.
- `Watch`de type `object` , que vous définissez sur une valeur à appliquer sur les plateformes de surveillance.
- `Converter`de type `IValueConverter` , qui peut être défini sur une `IValueConverter` implémentation.
- `ConverterParameter`de type `object` , qui peut être défini sur une valeur à passer à l' `IValueConverter` implémentation.

> [!NOTE]
> L’analyseur XAML permet [`OnIdiomExtension`](xref:Xamarin.Forms.Xaml.OnIdiomExtension) à la classe d’être abrégée comme `OnIdiom` .

La `Default` propriété est la propriété de contenu de `OnIdiomExtension` . Par conséquent, pour les expressions de balisage XAML exprimées à l’aide d’accolades, vous pouvez éliminer la `Default=` partie de l’expression à condition qu’il s’agisse du premier argument.

> [!IMPORTANT]
> L’analyseur XAML s’attend à ce que les valeurs du type correct soient fournies aux propriétés utilisant l' `OnIdiom` extension de balisage. Si la conversion de type est nécessaire, l' `OnIdiom` extension de balisage tente de l’exécuter à l’aide des convertisseurs par défaut fournis par Xamarin.Forms . Toutefois, certaines conversions de type ne peuvent pas être effectuées par les convertisseurs par défaut et, dans ce cas, la `Converter` propriété doit être définie sur une `IValueConverter` implémentation.

La page de **démonstration OnIdiom** montre comment utiliser l' `OnIdiom` extension de balisage :

```xaml
<BoxView Color="{OnIdiom Yellow, Phone=Red, Tablet=Green, Desktop=Blue}"
         WidthRequest="{OnIdiom 100, Phone=200, Tablet=300, Desktop=400}"
         HeightRequest="{OnIdiom 100, Phone=200, Tablet=300, Desktop=400}"
         HorizontalOptions="Center" />
```

Dans cet exemple, les trois `OnIdiom` expressions utilisent la version abrégée du nom de la `OnIdiomExtension` classe. Les trois `OnIdiom` extensions de balisage définissent les [`Color`](xref:Xamarin.Forms.BoxView.Color) [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) Propriétés, et [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) de la propriété sur des [`BoxView`](xref:Xamarin.Forms.BoxView) valeurs différentes sur les idiomes de téléphone, de tablette et de bureau. Les extensions de balisage fournissent également des valeurs par défaut pour ces propriétés sur les idiomes qui ne sont pas spécifiés, tout en éliminant la `Default=` partie de l’expression. Notez que les propriétés d’extension de balisage définies sont séparées par des virgules.

Voici le programme en cours d’exécution :

[![Démonstration OnIdiom](consuming-images/onidiomdemo-small.png "Démonstration OnIdiom")](consuming-images/onidiomdemo-large.png#lightbox "Démonstration OnIdiom")

## <a name="datatemplate-markup-extension"></a>DataTemplate (extension de balisage)

L' `DataTemplate` extension de balisage vous permet de convertir un type en [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) . Elle est prise en charge par la `DataTemplateExtension` classe, qui définit une `TypeName` propriété, de type `string` , qui est définie sur le nom du type à convertir en `DataTemplate` . La `TypeName` propriété est la propriété de contenu de `DataTemplateExtension` . Par conséquent, pour les expressions de balisage XAML exprimées à l’aide d’accolades, vous pouvez éliminer la `TypeName=` partie de l’expression.

> [!NOTE]
> L’analyseur XAML permet `DataTemplateExtension` à la classe d’être abrégée comme `DataTemplate` .

Une utilisation classique de cette extension de balisage se trouve dans une application d’environnement, comme illustré dans l’exemple suivant :

```xaml
<ShellContent Title="Monkeys"
              Icon="monkey.png"
              ContentTemplate="{DataTemplate views:MonkeysPage}" />
```

Dans cet exemple, `MonkeysPage` est converti d’un [`ContentPage`](xref:Xamarin.Forms.ContentPage) en [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) , qui est défini comme valeur de la `ShellContent.ContentTemplate` propriété. Cela permet de `MonkeysPage` s’assurer que est uniquement créé lorsque la navigation vers la page se produit, plutôt qu’au démarrage de l’application.

Pour plus d’informations sur les applications de l’interpréteur de commandes, consultez [ Xamarin.Forms Shell](~/xamarin-forms/app-fundamentals/shell/index.md).

## <a name="fontimage-markup-extension"></a>FontImage, extension de balisage

L' `FontImage` extension de balisage vous permet d’afficher une icône de police dans n’importe quelle vue pouvant afficher un `ImageSource` . Il fournit les mêmes fonctionnalités que la `FontImageSource` classe, mais avec une représentation plus concise.

L' `FontImage` extension de balisage est prise en charge par la `FontImageExtension` classe, qui définit les propriétés suivantes :

- `FontFamily`de type `string` , la famille de polices à laquelle appartient l’icône de police.
- `Glyph`de type `string` , la valeur de caractère Unicode de l’icône de police.
- `Color`de type [`Color`](xref:Xamarin.Forms.Color) , la couleur à utiliser lors de l’affichage de l’icône de police.
- `Size`de type `double` , la taille, en unités indépendantes du périphérique, de l’icône de police rendue. La valeur par défaut est 30. En outre, cette propriété peut être définie sur une taille de police nommée.

> [!NOTE]
> L’analyseur XAML permet `FontImageExtension` à la classe d’être abrégée comme `FontImage` .

La `Glyph` propriété est la propriété de contenu de `FontImageExtension` . Par conséquent, pour les expressions de balisage XAML exprimées à l’aide d’accolades, vous pouvez éliminer la `Glyph=` partie de l’expression à condition qu’il s’agisse du premier argument.

La page de **démonstration FontImage** montre comment utiliser l' `FontImage` extension de balisage :

```xaml
<Image BackgroundColor="#D1D1D1"
       Source="{FontImage &#xf30c;, FontFamily={OnPlatform iOS=Ionicons, Android=ionicons.ttf#}, Size=44}" />
```

Dans cet exemple, la version abrégée du nom de la `FontImageExtension` classe est utilisée pour afficher une icône Xbox, à partir de la famille de polices Ionicons, dans un [`Image`](xref:Xamarin.Forms.Image) . L’expression utilise également l' `OnPlatform` extension de balisage pour spécifier `FontFamily` des valeurs de propriété différentes sur iOS et Android. En outre, la `Glyph=` partie de l’expression est éliminée, et les propriétés d’extension de balisage définies sont séparées par des virgules. Notez que, si le caractère Unicode de l’icône est `\uf30c` , il doit être placé dans une séquence d’échappement en XAML et devient donc `&#xf30c;` .

Voici le programme en cours d’exécution :

[![Capture d’écran de l’extension de balisage FontImage](consuming-images/fontimagedemo.png "Démonstration FontImage")](consuming-images/fontimagedemo-large.png#lightbox "Démonstration FontImage")

Pour plus d’informations sur l’affichage des icônes de police en spécifiant les données d’icône de police dans un `FontImageSource` objet, consultez [afficher les icônes de police](~/xamarin-forms/user-interface/text/fonts.md#display-font-icons).

## <a name="appthemebinding-markup-extension"></a>Extension de balisage AppThemeBinding

L' `AppThemeBinding` extension de balisage vous permet de spécifier une ressource à consommer, telle qu’une image ou une couleur, en fonction du thème système actuel.

> [!IMPORTANT]
> L' `AppThemeBinding` extension de balisage a une configuration minimale requise pour le système d’exploitation. Pour plus d’informations, consultez [répondre aux modifications de thème du système dans les Xamarin.Forms applications](~/xamarin-forms/user-interface/theming/system-theme-changes.md).

L' `AppThemeBinding` extension de balisage est prise en charge par la `AppThemeBindingExtension` classe, qui définit les propriétés suivantes :

- `Default`, de type `object` , que vous définissez sur la ressource à utiliser par défaut.
- `Light`, de type `object` , que vous définissez sur la ressource à utiliser lorsque l’appareil utilise son thème clair.
- `Dark`, de type `object` , que vous définissez sur la ressource à utiliser lorsque l’appareil utilise son thème sombre.
- `Value`, de type `object` , qui retourne la ressource actuellement utilisée par l’extension de balisage.

> [!NOTE]
> L’analyseur XAML permet `AppThemeBindingExtension` à la classe d’être abrégée comme `AppBindingTheme` .

La `Default` propriété est la propriété de contenu de `AppThemeBindingExtension` . Par conséquent, pour les expressions de balisage XAML exprimées à l’aide d’accolades, vous pouvez éliminer la `Default=` partie de l’expression à condition qu’il s’agisse du premier argument.

La page de **démonstration AppThemeBinding** montre comment utiliser l' `AppThemeBinding` extension de balisage :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="MarkupExtensions.AppThemeBindingDemoPage"
             Title="AppThemeBinding Demo">
    <ContentPage.Resources>

        <Style x:Key="labelStyle"
               TargetType="Label">
            <Setter Property="TextColor"
                    Value="{AppThemeBinding Black, Light=Blue, Dark=Teal}" />
        </Style>

    </ContentPage.Resources>
    <StackLayout Margin="20">
        <Label Text="This text is green in light mode, and red in dark mode."
               TextColor="{AppThemeBinding Light=Green, Dark=Red}" />
        <Label Text="This text is black by default, blue in light mode, and teal in dark mode."
               Style="{StaticResource labelStyle}" />
    </StackLayout>
</ContentPage>
```

Dans cet exemple, la couleur de texte du premier [`Label`](xref:Xamarin.Forms.Label) est définie sur la couleur verte lorsque l’appareil utilise son thème clair, et est défini sur rouge lorsque l’appareil utilise son thème sombre. La deuxième `Label` a sa [`TextColor`](xref:Xamarin.Forms.Label.TextColor) propriété définie via un [`Style`](xref:Xamarin.Forms.Style) . Cela `Style` définit la couleur de texte du `Label` à noir par défaut, le bleu lorsque l’appareil utilise son thème clair et la couleur bleu-vert lorsque l’appareil utilise son thème sombre.

Voici le programme en cours d’exécution :

![Démonstration AppThemeBinding](consuming-images/appthemebindingdemo.png "Démonstration AppThemeBinding")

## <a name="define-markup-extensions"></a>Définir des extensions de balisage

Si vous avez eu besoin d’une extension de balisage XAML qui n’est pas disponible dans Xamarin.Forms , vous pouvez [créer vos propres](creating.md)extensions.

## <a name="related-links"></a>Liens connexes

- [Extensions de balisage (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-markupextensions)
- [Chapitre sur les extensions de balisage XAML du Xamarin.Forms livre](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter10.md)
- [Dictionnaires de ressources](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Styles dynamiques](~/xamarin-forms/user-interface/styles/dynamic.md)
- [Liaison de données](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [Xamarin.FormsShell](~/xamarin-forms/app-fundamentals/shell/index.md)
- [Répondre aux modifications de thème du système dans les Xamarin.Forms applications](~/xamarin-forms/user-interface/theming/system-theme-changes.md)
