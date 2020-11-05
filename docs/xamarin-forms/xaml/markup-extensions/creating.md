---
title: Création d’extensions de balisage XAML
description: Cet article explique comment définir vos propres extensions de Xamarin.Forms balisage XAML personnalisées. Une extension de balisage XAML est une classe qui implémente l’interface IMarkupExtension ou IMarkupExtension <T> .
ms.prod: xamarin
ms.assetid: 797C1EF9-1C8E-4208-8610-9B79CCF17D46
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 87153223c79365053bfc05b2a8a97c9aa1d4c4a4
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93369049"
---
# <a name="creating-xaml-markup-extensions"></a>Création d’extensions de balisage XAML

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](/samples/xamarin/xamarin-forms-samples/xaml-markupextensions)

Au niveau de la programmation, une extension de balisage XAML est une classe qui implémente l' [`IMarkupExtension`](xref:Xamarin.Forms.Xaml.IMarkupExtension) [`IMarkupExtension<T>`](xref:Xamarin.Forms.Xaml.IMarkupExtension`1) interface ou. Vous pouvez explorer le code source des extensions de balisage standard décrites ci-dessous dans le [répertoire **MarkupExtensions**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Xaml/MarkupExtensions) du Xamarin.Forms référentiel github.

Il est également possible de définir vos propres extensions de balisage XAML personnalisées en dérivant de `IMarkupExtension` ou de `IMarkupExtension<T>` . Utilisez le formulaire générique si l’extension de balisage obtient une valeur d’un type particulier. C’est le cas avec plusieurs extensions de Xamarin.Forms balisage :

- `TypeExtension` dérive de `IMarkupExtension<Type>`
- `ArrayExtension` dérive de `IMarkupExtension<Array>`
- `DynamicResourceExtension` dérive de `IMarkupExtension<DynamicResource>`
- `BindingExtension` dérive de `IMarkupExtension<BindingBase>`
- `ConstraintExpression` dérive de `IMarkupExtension<Constraint>`

Les deux `IMarkupExtension` interfaces définissent chacune une seule méthode, nommée `ProvideValue` :

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

Étant donné que `IMarkupExtension<T>` dérive de `IMarkupExtension` et inclut le `new` mot clé sur `ProvideValue` , il contient les deux `ProvideValue` méthodes.

Très souvent, les extensions de balisage XAML définissent les propriétés qui contribuent à la valeur de retour. (L’exception évidente est `NullExtension` , dans laquelle `ProvideValue` retourne simplement `null` .) La `ProvideValue` méthode a un argument unique de type `IServiceProvider` qui sera abordé plus loin dans cet article.

## <a name="a-markup-extension-for-specifying-color"></a>Extension de balisage pour la spécification de la couleur

L’extension de balisage XAML suivante vous permet de construire une `Color` valeur à l’aide de composants de teinte, de saturation et de luminosité. Il définit quatre propriétés pour les quatre composants de la couleur, y compris un composant alpha qui est initialisé à 1. La classe dérive de `IMarkupExtension<Color>` pour indiquer une `Color` valeur de retour :

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

Étant donné que `IMarkupExtension<T>` dérive de `IMarkupExtension` , la classe doit contenir deux `ProvideValue` méthodes, une qui retourne `Color` et une autre qui retourne `object` , mais la deuxième méthode peut simplement appeler la première méthode.

La page de démonstration de la **couleur TSL** affiche plusieurs méthodes qui `HslColorExtension` peuvent apparaître dans un fichier XAML pour spécifier la couleur d’un `BoxView` :

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

Notez que lorsque `HslColorExtension` est une balise XML, les quatre propriétés sont définies en tant qu’attributs, mais lorsqu’elles apparaissent entre accolades, les quatre propriétés sont séparées par des virgules sans guillemets. Les valeurs par défaut pour `H` , `S` et `L` sont 0, et la valeur par défaut de `A` est 1. par conséquent, ces propriétés peuvent être omises si vous souhaitez qu’elles soient définies sur les valeurs par défaut. Le dernier exemple montre un exemple dans lequel la luminosité est égale à 0, ce qui entraîne normalement un noir, mais le canal alpha est 0,5. il est donc mi-transparent et apparaît gris sur l’arrière-plan blanc de la page :

[![Démonstration de la couleur TSL](creating-images/hslcolordemo-small.png "Démonstration de la couleur TSL")](creating-images/hslcolordemo-large.png#lightbox "Démonstration de la couleur TSL")

## <a name="a-markup-extension-for-accessing-bitmaps"></a>Extension de balisage pour l’accès aux bitmaps

L’argument de `ProvideValue` est un objet qui implémente l' [`IServiceProvider`](xref:System.IServiceProvider) interface, qui est définie dans l' `System` espace de noms .net. Cette interface a un membre, une méthode nommée `GetService` avec un `Type` argument.

La `ImageResourceExtension` classe illustrée ci-dessous montre une utilisation possible de `IServiceProvider` et `GetService` pour obtenir un `IXmlLineInfoProvider` objet qui peut fournir des informations sur la ligne et le caractère indiquant où une erreur particulière a été détectée. Dans ce cas, une exception est levée lorsque la `Source` propriété n’a pas été définie :

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

`ImageResourceExtension` est utile lorsqu’un fichier XAML doit accéder à un fichier image stocké en tant que ressource incorporée dans le projet de bibliothèque de .NET Standard. Elle utilise la `Source` propriété pour appeler la `ImageSource.FromResource` méthode statique. Cette méthode requiert un nom de ressource complet, qui se compose du nom de l’assembly, du nom du dossier et du nom de fichier séparés par des points. Le deuxième argument de la `ImageSource.FromResource` méthode fournit le nom de l’assembly et n’est requis que pour les versions release sur UWP. Quoi qu’il en soit, `ImageSource.FromResource` doit être appelé à partir de l’assembly qui contient la bitmap, ce qui signifie que cette extension de ressource XAML ne peut pas faire partie d’une bibliothèque externe, sauf si les images se trouvent également dans cette bibliothèque. (Pour plus d’informations sur l’accès aux bitmaps stockées en tant que ressources incorporées, consultez l’article [**images incorporées**](~/xamarin-forms/user-interface/images.md#embedded-images) .)

Bien que `ImageResourceExtension` requiert la définition de la `Source` propriété, la `Source` propriété est indiquée dans un attribut en tant que propriété de contenu de la classe. Cela signifie que la `Source=` partie de l’expression entre accolades peut être omise. Dans la page de démonstration de la **ressource image** , les `Image` éléments extraient deux images en utilisant le nom du dossier et le nom de fichier séparés par des points :

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

[![Démonstration des ressources d’image](creating-images/imageresourcedemo-small.png "Démonstration des ressources d’image")](creating-images/imageresourcedemo-large.png#lightbox "Démonstration des ressources d’image")

## <a name="service-providers"></a>Fournisseurs de services

En utilisant l' `IServiceProvider` argument pour `ProvideValue` , les extensions de balisage XAML peuvent accéder à des informations utiles sur le fichier XAML dans lequel elles sont utilisées. Toutefois, pour utiliser `IServiceProvider` correctement l’argument, vous devez savoir quels sont les types de services disponibles dans des contextes particuliers. La meilleure façon d’acquérir une bonne compréhension de cette fonctionnalité consiste à étudier le code source des extensions de balisage XAML existantes dans le [dossier **MarkupExtensions**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Xaml/MarkupExtensions) du Xamarin.Forms référentiel sur GitHub. N’oubliez pas que certains types de services sont internes à Xamarin.Forms .

Dans certaines extensions de balisage XAML, ce service peut être utile :

```csharp
 IProvideValueTarget provideValueTarget = serviceProvider.GetService(typeof(IProvideValueTarget)) as IProvideValueTarget;
```

L' `IProvideValueTarget` interface définit deux propriétés, `TargetObject` et `TargetProperty` . Lorsque ces informations sont obtenues dans la `ImageResourceExtension` classe, `TargetObject` est le `Image` et `TargetProperty` est un `BindableProperty` objet pour la `Source` propriété de `Image` . Il s’agit de la propriété sur laquelle l’extension de balisage XAML a été définie.

L' `GetService` appel avec un argument de `typeof(IProvideValueTarget)` retourne en fait un objet de type `SimpleValueTargetProvider` , qui est défini dans l' `Xamarin.Forms.Xaml.Internals` espace de noms. Si vous effectuez un cast de la valeur de retour de `GetService` vers ce type, vous pouvez également accéder à une `ParentObjects` propriété, qui est un tableau qui contient l' `Image` élément, le `Grid` parent et le `ImageResourceDemoPage` parent du `Grid` .

## <a name="conclusion"></a>Conclusion

Les extensions de balisage XAML jouent un rôle essentiel dans XAML en étendant la possibilité de définir des attributs à partir de diverses sources. En outre, si les extensions de balisage XAML existantes ne fournissent pas exactement ce dont vous avez besoin, vous pouvez également écrire vos propres extensions.

## <a name="related-links"></a>Liens associés

- [Extensions de balisage (exemple)](/samples/xamarin/xamarin-forms-samples/xaml-markupextensions)
- [Chapitre sur les extensions de balisage XAML du Xamarin.Forms livre](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter10.md)