---
title: Création d’Extensions de balisage XAML
description: Cet article explique comment définir vos propres extensions de balisage XAML de Xamarin.Forms personnalisées. Une extension de balisage XAML est une classe qui implémente l’interface<T> IMarkupExtension ou IMarkupExtension.
ms.prod: xamarin
ms.assetid: 797C1EF9-1C8E-4208-8610-9B79CCF17D46
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: 4d26713f258a8c97abd4b4e9970ebdd4d490f485
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68655865"
---
# <a name="creating-xaml-markup-extensions"></a>Création d’extensions de balisage XAML

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-markupextensions)

Sur le niveau par programmation, une extension de balisage XAML est une classe qui implémente le [ `IMarkupExtension` ](xref:Xamarin.Forms.Xaml.IMarkupExtension) ou [ `IMarkupExtension<T>` ](xref:Xamarin.Forms.Xaml.IMarkupExtension`1) interface. Vous pouvez explorer le code source des extensions de balisage standard décrit ci-dessous dans le [ **MarkupExtensions** directory](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Xaml/MarkupExtensions) du référentiel GitHub de Xamarin.Forms.

Il est également possible de définir vos propres extensions de balisage XAML personnalisées en dérivant de `IMarkupExtension` ou `IMarkupExtension<T>`. Utilisez le formulaire générique si l’extension de balisage Obtient une valeur d’un type particulier. C’est le cas avec plusieurs Xamarin.Forms extensions de balisage :

- `TypeExtension` dérive de `IMarkupExtension<Type>`
- `ArrayExtension` dérive de `IMarkupExtension<Array>`
- `DynamicResourceExtension` dérive de `IMarkupExtension<DynamicResource>`
- `BindingExtension` dérive de `IMarkupExtension<BindingBase>`
- `ConstraintExpression` dérive de `IMarkupExtension<Constraint>`

Les deux `IMarkupExtension` interfaces ne définissent qu’une seule méthode nommé `ProvideValue`:

```csharp
public interface IMarkupExtension
{
    object ProvideValue(IServiceProvider serviceProvider);
}

public interface IMarkupExtension<out T> : IMarkupExtension
{
    new T ProvideValue(IServiceProvider serviceProvider);
}
```

Dans la mesure où `IMarkupExtension<T>` dérive `IMarkupExtension` et inclut le `new` mot clé sur `ProvideValue`, il contient à la fois `ProvideValue` méthodes.

Très souvent, les extensions de balisage XAML définissant des propriétés qui contribuent à la valeur de retour. (L’exception évidente est `NullExtension`, dans lequel `ProvideValue` renvoie simplement `null`.) Le `ProvideValue` méthode possède un argument unique de type `IServiceProvider` qui seront abordées plus loin dans cet article.

## <a name="a-markup-extension-for-specifying-color"></a>Une Extension de balisage pour la spécification de couleur

L’extension de balisage XAML suivante vous permet de construire un `Color` valeur à l’aide de composants de teinte, saturation et de luminosité. Il définit quatre propriétés pour les quatre composants de la couleur, y compris un composant alpha est initialisé à 1. La classe dérive `IMarkupExtension<Color>` pour indiquer un `Color` valeur de retour :

```csharp
public class HslColorExtension : IMarkupExtension<Color>
{
    public double H { set; get; }

    public double S { set; get; }

    public double L { set; get; }

    public double A { set; get; } = 1.0;

    public Color ProvideValue(IServiceProvider serviceProvider)
    {
        return Color.FromHsla(H, S, L, A);
    }

    object IMarkupExtension.ProvideValue(IServiceProvider serviceProvider)
    {
        return (this as IMarkupExtension<Color>).ProvideValue(serviceProvider);
    }
}
```

Étant donné que `IMarkupExtension<T>` dérive `IMarkupExtension`, la classe doit contenir deux `ProvideValue` méthodes, celui qui retourne `Color` et une autre qui retourne `object`, mais la seconde méthode peut simplement appeler la première méthode.

Le **démonstration de couleurs TSL** page montre plusieurs façons qui `HslColorExtension` peut apparaître dans un fichier XAML pour spécifier la couleur pour une `BoxView`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:MarkupExtensions"
             x:Class="MarkupExtensions.HslColorDemoPage"
             Title="HSL Color Demo">

    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="BoxView">
                <Setter Property="WidthRequest" Value="80" />
                <Setter Property="HeightRequest" Value="80" />
                <Setter Property="HorizontalOptions" Value="Center" />
                <Setter Property="VerticalOptions" Value="CenterAndExpand" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout>
        <BoxView>
            <BoxView.Color>
                <local:HslColorExtension H="0" S="1" L="0.5" A="1" />
            </BoxView.Color>
        </BoxView>

        <BoxView>
            <BoxView.Color>
                <local:HslColor H="0.33" S="1" L="0.5" />
            </BoxView.Color>
        </BoxView>

        <BoxView Color="{local:HslColorExtension H=0.67, S=1, L=0.5}" />

        <BoxView Color="{local:HslColor H=0, S=0, L=0.5}" />

        <BoxView Color="{local:HslColor A=0.5}" />
    </StackLayout>
</ContentPage>
```

Notez que lorsque `HslColorExtension` est une balise XML, les quatre propriétés sont définies en tant qu’attributs, mais lorsqu’il apparaît entre accolades, les quatre propriétés sont séparées par des points-virgules sans guillemets. Les valeurs par défaut pour `H`, `S`, et `L` sont 0 et la valeur par défaut de `A` est 1, ces propriétés peuvent être omises si vous souhaitez que les valeurs par défaut. Le dernier exemple montre un exemple où la luminosité est 0, ce qui aboutit généralement à noir, mais le canal alpha est de 0,5, il est donc six transparent et s’affiche en gris par rapport à l’arrière-plan blanc de la page :

[![Démonstration de couleurs TSL](creating-images/hslcolordemo-small.png "démonstration de couleurs TSL")](creating-images/hslcolordemo-large.png#lightbox "démonstration de couleur TSL")

## <a name="a-markup-extension-for-accessing-bitmaps"></a>Une Extension de balisage pour l’accès à des Bitmaps

L’argument `ProvideValue` est un objet qui implémente le [ `IServiceProvider` ](xref:System.IServiceProvider) interface, ce qui est défini dans le .NET `System` espace de noms. Cette interface a un seul membre, une méthode nommée `GetService` avec un `Type` argument.

Le `ImageResourceExtension` classe ci-dessous illustre une utilisation possible de `IServiceProvider` et `GetService` pour obtenir un `IXmlLineInfoProvider` objet qui peut fournir des informations de ligne et le caractère indiquant où une erreur particulière a été détectée. Dans ce cas, une exception est levée lorsque le `Source` propriété n’a pas été définie :

```csharp
[ContentProperty("Source")]
class ImageResourceExtension : IMarkupExtension<ImageSource>
{
    public string Source { set; get; }

    public ImageSource ProvideValue(IServiceProvider serviceProvider)
    {
        if (String.IsNullOrEmpty(Source))
        {
            IXmlLineInfoProvider lineInfoProvider = serviceProvider.GetService(typeof(IXmlLineInfoProvider)) as IXmlLineInfoProvider;
            IXmlLineInfo lineInfo = (lineInfoProvider != null) ? lineInfoProvider.XmlLineInfo : new XmlLineInfo();
            throw new XamlParseException("ImageResourceExtension requires Source property to be set", lineInfo);
        }

        string assemblyName = GetType().GetTypeInfo().Assembly.GetName().Name;
        return ImageSource.FromResource(assemblyName + "." + Source, typeof(ImageResourceExtension).GetTypeInfo().Assembly);
    }

    object IMarkupExtension.ProvideValue(IServiceProvider serviceProvider)
    {
        return (this as IMarkupExtension<ImageSource>).ProvideValue(serviceProvider);
    }
}
```

`ImageResourceExtension` est utile lorsqu’un fichier XAML doit accéder à un fichier image stocké comme une ressource incorporée dans le projet de bibliothèque .NET Standard. Il utilise le `Source` propriété pour appeler la méthode statique `ImageSource.FromResource` (méthode). Cette méthode requiert un nom de ressource complet, qui comprend le nom de l’assembly, le nom du dossier et le nom de fichier séparées par des points. Le deuxième argument de la `ImageSource.FromResource` méthode fournit le nom de l’assembly et est uniquement requis pour les versions release sur UWP. Malgré tout, `ImageSource.FromResource` doit être appelé à partir de l’assembly qui contient l’image bitmap, ce qui signifie que cette extension de ressource XAML ne peut pas faire partie d’une bibliothèque externe, sauf si les images sont également dans cette bibliothèque. (Consultez le [ **Images incorporées** ](~/xamarin-forms/user-interface/images.md#embedded-images) article pour plus d’informations sur l’accès aux bitmaps stockés en tant que ressources incorporées.)

Bien que `ImageResourceExtension` nécessite le `Source` propriété à définir, le `Source` propriété est indiquée dans un attribut en tant que la propriété de contenu de la classe. Cela signifie que la `Source=` partie de l’expression entre accolades peut être omis. Dans le **démonstration de ressource d’Image** page, le `Image` éléments extraire en utilisant le nom du dossier et le nom de fichier séparées par des points de deux images :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:MarkupExtensions"
             x:Class="MarkupExtensions.ImageResourceDemoPage"
             Title="Image Resource Demo">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Image Source="{local:ImageResource Images.SeatedMonkey.jpg}"
               Grid.Row="0" />

        <Image Source="{local:ImageResource Images.FacePalm.jpg}"
               Grid.Row="1" />

    </Grid>
</ContentPage>
```

Voici le programme en cours d’exécution :

[![Démonstration de la ressource d’image](creating-images/imageresourcedemo-small.png "démonstration de la ressource d’Image")](creating-images/imageresourcedemo-large.png#lightbox "démonstration de la ressource d’Image")

## <a name="service-providers"></a>Fournisseurs de services

À l’aide de la `IServiceProvider` l’argument de `ProvideValue`, les extensions de balisage XAML peuvent accéder aux informations utiles sur le fichier XAML dans lequel ils sont utilisés. Mais à utiliser le `IServiceProvider` argument avec succès, vous devez savoir quelles sont les services sont disponibles dans des contextes particuliers. La meilleure façon de comprendre cette fonctionnalité consiste à étudier le code source des extensions de balisage XAML existants dans le [ **MarkupExtensions** dossier](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Xaml/MarkupExtensions) dans le référentiel de Xamarin.Forms sur GitHub. N’oubliez pas que certains types de services sont internes à Xamarin.Forms.

Dans certaines extensions de balisage XAML, ce service peut être utile :

```csharp
 IProvideValueTarget provideValueTarget = serviceProvider.GetService(typeof(IProvideValueTarget)) as IProvideValueTarget;
```

Le `IProvideValueTarget` interface définit deux propriétés, `TargetObject` et `TargetProperty`. Lorsque ces informations sont obtenues dans le `ImageResourceExtension` (classe), `TargetObject` est la `Image` et `TargetProperty` est un `BindableProperty` de l’objet pour le `Source` propriété de `Image`. Il s’agit de la propriété sur laquelle l’extension de balisage XAML a été définie.

Le `GetService` appel avec un argument de `typeof(IProvideValueTarget)` renvoie en fait un objet de type `SimpleValueTargetProvider`, qui est défini dans le `Xamarin.Forms.Xaml.Internals` espace de noms. Si vous effectuez un cast de la valeur de retour de `GetService` à ce type, vous pouvez également accéder à un `ParentObjects` propriété, qui est un tableau qui contient le `Image` élément, le `Grid` parent et le `ImageResourceDemoPage` parent de la `Grid`.

## <a name="conclusion"></a>Conclusion

Extensions de balisage XAML jouent un rôle essentiel dans XAML en étendant la possibilité de définir les attributs à partir de diverses sources. En outre, si les extensions de balisage XAML existantes ne fournissent pas exactement ce dont vous avez besoin, vous pouvez également écrire votre propre.

## <a name="related-links"></a>Liens associés

- [Extensions de balisage (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-markupextensions)
- [Chapitre d’extensions de balisage XAML de Xamarin.Forms livre](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter10.md)
