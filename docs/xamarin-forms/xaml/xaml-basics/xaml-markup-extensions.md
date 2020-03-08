---
title: Partie 3. Extensions de balisage XAML
description: Extensions de balisage XAML constituent une fonctionnalité importante dans XAML qui permettent d’être définie sur des objets ou des valeurs qui sont référencés indirectement à partir d’autres sources.
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F4A37564-B18B-42FF-B841-9A1949895AB6
author: davidbritch
ms.author: dabritch
ms.date: 03/27/2018
ms.openlocfilehash: 89e2026ff16a9614234d6ee4bfa4df620cf58b56
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2020
ms.locfileid: "78917648"
---
# <a name="part-3-xaml-markup-extensions"></a>Partie 3. Extensions de balisage XAML

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)

_Les extensions de balisage XAML constituent une fonctionnalité importante du langage XAML qui permet de définir des propriétés sur des objets ou des valeurs qui sont référencés indirectement à partir d’autres sources. Les extensions de balisage XAML sont particulièrement importantes pour le partage d’objets et le référencement des constantes utilisées dans une application, mais elles trouvent leur plus grand utilitaire dans les liaisons de données._

## <a name="xaml-markup-extensions"></a>Extensions de balisage XAML

En règle générale, vous utilisez XAML pour définir les propriétés d’un objet à des valeurs explicites, comme une chaîne, un nombre, un membre d’énumération ou une chaîne est convertie en une valeur dans les coulisses.

Parfois, cependant, propriétés doivent référencer à la place des valeurs définis à un endroit autre ou qui peut nécessiter un peu de traitement par le code lors de l’exécution. À ces fins, les *extensions de balisage* XAML sont disponibles.

Ces extensions de balisage XAML ne sont pas des extensions de code XML. XAML est entièrement juridique XML. Ils sont appelés « extensions », car ils sont soutenus par du code dans les classes qui implémentent `IMarkupExtension`. Vous pouvez écrire vos propres extensions de balisage personnalisée.

Dans de nombreux cas, les extensions de balisage XAML sont instantanément reconnaissables dans les fichiers XAML parce qu’ils apparaissent en tant que paramètres d’attribut délimitées par des accolades : {et}, mais parfois les extensions de balisage apparaissent dans le balisage en tant qu’éléments conventionnels.

## <a name="shared-resources"></a>Ressources partagées

Certaines pages XAML contient plusieurs vues avec les mêmes valeurs de propriétés. Par exemple, la plupart des paramètres de propriété pour ces objets `Button` sont les mêmes :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SharedResourcesPage"
             Title="Shared Resources Page">

    <StackLayout>
        <Button Text="Do this!"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                BorderWidth="3"
                Rotation="-15"
                TextColor="Red"
                FontSize="24" />

        <Button Text="Do that!"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                BorderWidth="3"
                Rotation="-15"
                TextColor="Red"
                FontSize="24" />

        <Button Text="Do the other thing!"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                BorderWidth="3"
                Rotation="-15"
                TextColor="Red"
                FontSize="24" />

    </StackLayout>
</ContentPage>
```

Si une de ces propriétés doit être modifiée, vous préférerez peut-être la modification qu’une seule fois plutôt que trois fois. S’il s’agissait de code, vous serez probablement être à l’aide de constantes et les objets statiques en lecture seule pour aider à maintenir ces valeurs cohérentes et faciles à modifier.

En XAML, une solution courante consiste à stocker des valeurs ou des objets de ce type dans un *dictionnaire de ressources*. La classe `VisualElement` définit une propriété nommée `Resources` de type `ResourceDictionary`, qui est un dictionnaire avec des clés de type `string` et des valeurs de type `object`. Vous pouvez placer des objets dans ce dictionnaire et ensuite les référencer à partir du balisage, tout en XAML.

Pour utiliser un dictionnaire de ressources sur une page, incluez une paire de balises d’éléments de propriété `Resources`. Il est plus commode de placer ces éléments en haut de la page :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SharedResourcesPage"
             Title="Shared Resources Page">

    <ContentPage.Resources>

    </ContentPage.Resources>
    ...
</ContentPage>
```

Il est également nécessaire d’inclure explicitement les balises `ResourceDictionary` :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SharedResourcesPage"
             Title="Shared Resources Page">

    <ContentPage.Resources>
        <ResourceDictionary>

        </ResourceDictionary>
    </ContentPage.Resources>
    ...
</ContentPage>
```

Maintenant objets et les valeurs de différents types peuvent être ajoutés pour le dictionnaire de ressources. Ces types doivent être instanciables. Ils ne peuvent pas être des classes abstraites, par exemple. Ces types doivent avoir également d’un constructeur sans paramètre public. Chaque élément requiert une clé de dictionnaire spécifiée avec l’attribut `x:Key`. Par exemple :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SharedResourcesPage"
             Title="Shared Resources Page">

    <ContentPage.Resources>
        <ResourceDictionary>
            <LayoutOptions x:Key="horzOptions"
                           Alignment="Center" />

            <LayoutOptions x:Key="vertOptions"
                           Alignment="Center"
                           Expands="True" />
        </ResourceDictionary>
    </ContentPage.Resources>
    ...
</ContentPage>
```

Ces deux éléments sont des valeurs de type structure `LayoutOptions`, et chacun a une clé unique et une ou deux propriétés définies. Dans le code et le balisage, il est bien plus courant d’utiliser les champs statiques de `LayoutOptions`, mais ici, il est plus pratique de définir les propriétés.

Il est maintenant nécessaire de définir les propriétés `HorizontalOptions` et `VerticalOptions` de ces boutons sur ces ressources, ce qui est fait avec l’extension de balisage XAML `StaticResource` :

```xaml
<Button Text="Do this!"
        HorizontalOptions="{StaticResource horzOptions}"
        VerticalOptions="{StaticResource vertOptions}"
        BorderWidth="3"
        Rotation="-15"
        TextColor="Red"
        FontSize="24" />
```

L’extension de balisage `StaticResource` est toujours délimitée par des accolades et comprend la clé de dictionnaire.

Le nom `StaticResource` le distingue de `DynamicResource`, que Xamarin. Forms prend également en charge. `DynamicResource` concerne les clés de dictionnaire associées à des valeurs qui peuvent changer pendant l’exécution, tandis que `StaticResource` accède à des éléments du dictionnaire une seule fois lorsque les éléments de la page sont construits.

Pour la propriété `BorderWidth`, il est nécessaire de stocker un double dans le dictionnaire. XAML définit facilement des balises pour les types de données courants comme `x:Double` et `x:Int32`:

```xaml
<ContentPage.Resources>
    <ResourceDictionary>
        <LayoutOptions x:Key="horzOptions"
                       Alignment="Center" />

        <LayoutOptions x:Key="vertOptions"
                       Alignment="Center"
                       Expands="True" />

        <x:Double x:Key="borderWidth">
            3
        </x:Double>
    </ResourceDictionary>
</ContentPage.Resources>
```

Vous n’avez pas besoin de placer sur trois lignes. Cette entrée de dictionnaire pour cet angle de rotation occupe uniquement une seule ligne :

```xaml
<ContentPage.Resources>
    <ResourceDictionary>
        <LayoutOptions x:Key="horzOptions"
                       Alignment="Center" />

        <LayoutOptions x:Key="vertOptions"
                       Alignment="Center"
                       Expands="True" />

         <x:Double x:Key="borderWidth">
            3
         </x:Double>

        <x:Double x:Key="rotationAngle">-15</x:Double>
    </ResourceDictionary>
</ContentPage.Resources>
```

Ces deux ressources peuvent être référencées de la même façon que les valeurs de `LayoutOptions` :

```xaml
<Button Text="Do this!"
        HorizontalOptions="{StaticResource horzOptions}"
        VerticalOptions="{StaticResource vertOptions}"
        BorderWidth="{StaticResource borderWidth}"
        Rotation="{StaticResource rotationAngle}"
        TextColor="Red"
        FontSize="24" />
```

Pour les ressources de type `Color`, vous pouvez utiliser les mêmes représentations sous forme de chaîne que celles que vous utilisez pour assigner directement des attributs de ces types. Les convertisseurs de type sont appelées lorsque la ressource est créée. Voici une ressource de type `Color`:

```xaml
<Color x:Key="textColor">Red</Color>
```

Souvent, les programmes définissent une propriété `FontSize` sur un membre de l’énumération `NamedSize` comme `Large`. La classe `FontSizeConverter` fonctionne en arrière-plan pour la convertir en une valeur dépendante de la plateforme à l’aide de la méthode `Device.GetNamedSized`. Toutefois, lors de la définition d’une ressource de taille de police, il est plus judicieux d’utiliser une valeur numérique, indiquée ici en tant que type de `x:Double` :

```xaml
<x:Double x:Key="fontSize">24</x:Double>
```

Désormais, toutes les propriétés, à l’exception des `Text`, sont définies par les paramètres de ressource :

```xaml
<Button Text="Do this!"
        HorizontalOptions="{StaticResource horzOptions}"
        VerticalOptions="{StaticResource vertOptions}"
        BorderWidth="{StaticResource borderWidth}"
        Rotation="{StaticResource rotationAngle}"
        TextColor="{StaticResource textColor}"
        FontSize="{StaticResource fontSize}" />
```

Il est également possible d’utiliser `OnPlatform` dans le dictionnaire de ressources pour définir différentes valeurs pour les plateformes. Voici comment un objet `OnPlatform` peut faire partie du dictionnaire de ressources pour différentes couleurs de texte :

```xaml
<OnPlatform x:Key="textColor"
            x:TypeArguments="Color">
    <On Platform="iOS" Value="Red" />
    <On Platform="Android" Value="Aqua" />
    <On Platform="UWP" Value="#80FF80" />
</OnPlatform>
```

Notez que `OnPlatform` obtient un attribut `x:Key` parce qu’il s’agit d’un objet dans le dictionnaire et d’un attribut `x:TypeArguments`, car il s’agit d’une classe générique. Les attributs `iOS`, `Android`et `UWP` sont convertis en `Color` valeurs lorsque l’objet est initialisé.

Voici le fichier XAML complet final avec trois boutons, l’accès aux six valeurs partagés :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SharedResourcesPage"
             Title="Shared Resources Page">

    <ContentPage.Resources>
        <ResourceDictionary>
            <LayoutOptions x:Key="horzOptions"
                           Alignment="Center" />

            <LayoutOptions x:Key="vertOptions"
                           Alignment="Center"
                           Expands="True" />

            <x:Double x:Key="borderWidth">3</x:Double>

            <x:Double x:Key="rotationAngle">-15</x:Double>

            <OnPlatform x:Key="textColor"
                        x:TypeArguments="Color">
                <On Platform="iOS" Value="Red" />
                <On Platform="Android" Value="Aqua" />
                <On Platform="UWP" Value="#80FF80" />
            </OnPlatform>

            <x:Double x:Key="fontSize">24</x:Double>
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout>
        <Button Text="Do this!"
                HorizontalOptions="{StaticResource horzOptions}"
                VerticalOptions="{StaticResource vertOptions}"
                BorderWidth="{StaticResource borderWidth}"
                Rotation="{StaticResource rotationAngle}"
                TextColor="{StaticResource textColor}"
                FontSize="{StaticResource fontSize}" />

        <Button Text="Do that!"
                HorizontalOptions="{StaticResource horzOptions}"
                VerticalOptions="{StaticResource vertOptions}"
                BorderWidth="{StaticResource borderWidth}"
                Rotation="{StaticResource rotationAngle}"
                TextColor="{StaticResource textColor}"
                FontSize="{StaticResource fontSize}" />

        <Button Text="Do the other thing!"
                HorizontalOptions="{StaticResource horzOptions}"
                VerticalOptions="{StaticResource vertOptions}"
                BorderWidth="{StaticResource borderWidth}"
                Rotation="{StaticResource rotationAngle}"
                TextColor="{StaticResource textColor}"
                FontSize="{StaticResource fontSize}" />

    </StackLayout>
</ContentPage>
```

Les captures d’écran vérifier la cohérence du style et le style dépend de la plateforme :

[Contrôles de style ![](xaml-markup-extensions-images/sharedresources.png)](xaml-markup-extensions-images/sharedresources-large.png#lightbox)

Bien qu’il soit plus courant de définir la collection `Resources` en haut de la page, gardez à l’esprit que la propriété `Resources` est définie par `VisualElement`, et que vous pouvez avoir `Resources` collections sur d’autres éléments de la page. Par exemple, essayez d’en ajouter une au `StackLayout` dans cet exemple :

```xaml
<StackLayout>
    <StackLayout.Resources>
        <ResourceDictionary>
            <Color x:Key="textColor">Blue</Color>
        </ResourceDictionary>
    </StackLayout.Resources>
    ...
</StackLayout>
```

Vous découvrirez que la couleur du texte des boutons est désormais bleu. Fondamentalement, chaque fois que l’analyseur XAML rencontre une `StaticResource` extension de balisage, il recherche dans l’arborescence d’éléments visuels et utilise la première `ResourceDictionary` qu’il rencontre avec cette clé.

L’un des types d’objets les plus courants stockés dans les dictionnaires de ressources est le `Style`Xamarin. Forms, qui définit une collection de paramètres de propriété. Les styles sont présentés dans les [styles](~/xamarin-forms/user-interface/styles/index.md)d’article.

Parfois, les développeurs qui débutent avec XAML se demandent s’ils peuvent placer un élément visuel tel que `Label` ou `Button` dans un `ResourceDictionary`. Bien qu’il soit certainement possible, il n’y beaucoup de sens. L’objectif de l' `ResourceDictionary` est de partager des objets. Un élément visuel ne peut pas être partagé. La même instance ne peut pas apparaître deux fois sur une seule page.

## <a name="the-xstatic-markup-extension"></a>L’Extension de balisage x : Static

Malgré les similitudes de leurs noms, les `x:Static` et les `StaticResource` sont très différents. `StaticResource` retourne un objet à partir d’un dictionnaire de ressources alors que `x:Static` accède à l’un des éléments suivants :

- un champ statique public
- une propriété statique publique
- un champ constant publique
- un membre d’énumération.

L’extension de balisage `StaticResource` est prise en charge par les implémentations XAML qui définissent un dictionnaire de ressources, tandis que `x:Static` est une partie intrinsèque du code XAML, comme le montre le préfixe `x`.

Voici quelques exemples qui illustrent comment `x:Static` peut explicitement référencer des champs statiques et des membres de l’énumération :

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="{x:Static LayoutOptions.Start}"
       HorizontalTextAlignment="{x:Static TextAlignment.Center}"
       TextColor="{x:Static Color.Aqua}" />
```

Jusqu’ici, ce n’est pas très impressionnant. Toutefois, l’extension de balisage `x:Static` peut également faire référence à des champs ou des propriétés statiques à partir de votre propre code. Par exemple, voici un `AppConstants` classe qui contient des champs statiques que vous pouvez utiliser sur plusieurs pages dans une application :

```csharp
using System;
using Xamarin.Forms;

namespace XamlSamples
{
    static class AppConstants
    {
        public static readonly Thickness PagePadding;

        public static readonly Font TitleFont;

        public static readonly Color BackgroundColor = Color.Aqua;

        public static readonly Color ForegroundColor = Color.Brown;

        static AppConstants()
        {
            switch (Device.RuntimePlatform)
            {
                case Device.iOS:
                    PagePadding = new Thickness(5, 20, 5, 0);
                    TitleFont = Font.SystemFontOfSize(35, FontAttributes.Bold);
                    break;

                case Device.Android:
                    PagePadding = new Thickness(5, 0, 5, 0);
                    TitleFont = Font.SystemFontOfSize(40, FontAttributes.Bold);
                    break;

                case Device.UWP:
                    PagePadding = new Thickness(5, 0, 5, 0);
                    TitleFont = Font.SystemFontOfSize(50, FontAttributes.Bold);
                    break;
            }
        }
    }
}
```

Pour référencer les champs statiques de cette classe dans le fichier XAML, vous aurez besoin d’un moyen d’indiquer dans le fichier XAML où se trouve ce fichier. Pour cela, avec une déclaration d’espace de noms XML.

Rappelez-vous que les fichiers XAML créés en tant que partie du modèle Xamarin.Forms XAML standard contiennent deux déclarations d’espace de noms XML : un pour l’accès à des classes de Xamarin.Forms et l’autre pour le référencement des balises et des attributs XAML intrinsèques :

```csharp
xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
```

Vous aurez besoin des déclarations d’espace de noms XML supplémentaires pour accéder aux autres classes. Chaque déclaration d’espace de noms XML supplémentaire définit un nouveau préfixe. Pour accéder aux classes locales de la bibliothèque de .NET Standard d’applications partagées, comme `AppConstants`, les programmeurs XAML utilisent souvent le préfixe `local`. La déclaration d’espace de noms doit indiquer le nom de l’espace de noms CLR (Common Language Runtime), également appelé nom de l’espace de noms .NET C# , qui est le nom qui apparaît dans une définition de `namespace` ou dans une directive `using` :

```csharp
xmlns:local="clr-namespace:XamlSamples"
```

Vous pouvez également définir des déclarations d’espace de noms XML pour les espaces de noms .NET dans n’importe quel assembly fait référence à la bibliothèque .NET Standard. Par exemple, voici un préfixe `sys` pour l’espace de noms .NET `System` standard, qui se trouve dans l’assembly **netstandard** . Étant donné qu’il s’agit d’un autre assembly, vous devez également spécifier le nom de l’assembly, dans ce cas **netstandard**:

```csharp
xmlns:sys="clr-namespace:System;assembly=netstandard"
```

Notez que le mot clé `clr-namespace` est suivi d’un signe deux-points, puis du nom de l’espace de noms .NET, suivi d’un point-virgule, du mot clé `assembly`, d’un signe égal et du nom de l’assembly.

Oui, un signe deux-points suit `clr-namespace` mais le signe égal suit `assembly`. La syntaxe a été définie de cette façon délibérée : la plupart des déclarations d’espaces de noms XML référencent un URI qui commence un nom de modèle d’URI tel que `http`, qui est toujours suivi de deux-points. La `clr-namespace` partie de cette chaîne est destinée à imiter cette Convention.

Ces deux déclarations d’espaces de noms sont incluses dans l’exemple **StaticConstantsPage** . Notez que les dimensions de `BoxView` sont définies sur `Math.PI` et `Math.E`, mais mises à l’échelle selon un facteur de 100 :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples"
             xmlns:sys="clr-namespace:System;assembly=netstandard"
             x:Class="XamlSamples.StaticConstantsPage"
             Title="Static Constants Page"
             Padding="{x:Static local:AppConstants.PagePadding}">

    <StackLayout>
       <Label Text="Hello, XAML!"
              TextColor="{x:Static local:AppConstants.BackgroundColor}"
              BackgroundColor="{x:Static local:AppConstants.ForegroundColor}"
              Font="{x:Static local:AppConstants.TitleFont}"
              HorizontalOptions="Center" />

      <BoxView WidthRequest="{x:Static sys:Math.PI}"
               HeightRequest="{x:Static sys:Math.E}"
               Color="{x:Static local:AppConstants.ForegroundColor}"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand"
               Scale="100" />
    </StackLayout>
</ContentPage>
```

La taille des `BoxView` résultantes par rapport à l’écran dépend de la plateforme :

[Contrôles ![utilisant l’extension de balisage x :Static](xaml-markup-extensions-images/staticconstants.png)](xaml-markup-extensions-images/staticconstants-large.png#lightbox)

## <a name="other-standard-markup-extensions"></a>Autres Extensions de balisage Standard

Plusieurs extensions de balisage sont intrinsèques à XAML et pris en charge dans les fichiers XAML de Xamarin.Forms. Certaines d'entre elles ne sont pas très souvent utilisés, mais sont essentielles lorsque vous en avez besoin :

- Si une propriété a une valeur non `null` par défaut, mais que vous souhaitez la définir sur `null`, définissez-la sur l’extension de balisage `{x:Null}`.
- Si une propriété est de type `Type`, vous pouvez l’assigner à un objet `Type` à l’aide de l’extension de balisage `{x:Type someClass}`.
- Vous pouvez définir des tableaux en XAML à l’aide de l’extension de balisage `x:Array`. Cette extension de balisage a un attribut obligatoire nommé `Type` qui indique le type des éléments dans le tableau.
- L’extension de balisage `Binding` est présentée dans la [partie 4. Notions de base](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)de la liaison de données.
- L’extension de balisage `RelativeSource` est présentée dans [liaisons relatives](~/xamarin-forms/app-fundamentals/data-binding/relative-bindings.md).

## <a name="the-constraintexpression-markup-extension"></a>L’Extension de balisage de ConstraintExpression

Extensions de balisage peuvent avoir des propriétés, mais elles ne sont pas définies comme attributs XML. Dans une extension de balisage, les paramètres de propriété sont séparés par des virgules, et sans les guillemets apparaissent entre accolades.

Cela peut être illustré avec l’extension de balisage Xamarin. Forms nommée `ConstraintExpression`, qui est utilisée avec la classe `RelativeLayout`. Vous pouvez spécifier l’emplacement ou la taille d’une vue enfant en tant que constante ou par rapport à un parent ou une autre vue nommée. La syntaxe de la `ConstraintExpression` vous permet de définir la position ou la taille d’une vue à l’aide d’un `Factor` fois une propriété d’une autre vue, ainsi qu’une `Constant`. Toute opération plus complexe que celle nécessite du code.

Voici un exemple :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.RelativeLayoutPage"
             Title="RelativeLayout Page">

    <RelativeLayout>

        <!-- Upper left -->
        <BoxView Color="Red"
                 RelativeLayout.XConstraint=
                     "{ConstraintExpression Type=Constant,
                                            Constant=0}"
                 RelativeLayout.YConstraint=
                     "{ConstraintExpression Type=Constant,
                                            Constant=0}" />
        <!-- Upper right -->
        <BoxView Color="Green"
                 RelativeLayout.XConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Width,
                                            Factor=1,
                                            Constant=-40}"
                 RelativeLayout.YConstraint=
                     "{ConstraintExpression Type=Constant,
                                            Constant=0}" />
        <!-- Lower left -->
        <BoxView Color="Blue"
                 RelativeLayout.XConstraint=
                     "{ConstraintExpression Type=Constant,
                                            Constant=0}"
                 RelativeLayout.YConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Height,
                                            Factor=1,
                                            Constant=-40}" />
        <!-- Lower right -->
        <BoxView Color="Yellow"
                 RelativeLayout.XConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Width,
                                            Factor=1,
                                            Constant=-40}"
                 RelativeLayout.YConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Height,
                                            Factor=1,
                                            Constant=-40}" />

        <!-- Centered and 1/3 width and height of parent -->
        <BoxView x:Name="oneThird"
                 Color="Red"
                 RelativeLayout.XConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Width,
                                            Factor=0.33}"
                 RelativeLayout.YConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Height,
                                            Factor=0.33}"
                 RelativeLayout.WidthConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Width,
                                            Factor=0.33}"
                 RelativeLayout.HeightConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Height,
                                            Factor=0.33}"  />

        <!-- 1/3 width and height of previous -->
        <BoxView Color="Blue"
                 RelativeLayout.XConstraint=
                     "{ConstraintExpression Type=RelativeToView,
                                            ElementName=oneThird,
                                            Property=X}"
                 RelativeLayout.YConstraint=
                     "{ConstraintExpression Type=RelativeToView,
                                            ElementName=oneThird,
                                            Property=Y}"
                 RelativeLayout.WidthConstraint=
                     "{ConstraintExpression Type=RelativeToView,
                                            ElementName=oneThird,
                                            Property=Width,
                                            Factor=0.33}"
                 RelativeLayout.HeightConstraint=
                     "{ConstraintExpression Type=RelativeToView,
                                            ElementName=oneThird,
                                            Property=Height,
                                            Factor=0.33}"  />
    </RelativeLayout>
</ContentPage>
```

La leçon plus importantes à suivre à partir de cet exemple est sans doute la syntaxe de l’extension de balisage : aucun guillemets ne doit apparaître entre les accolades d’une extension de balisage. Lorsque vous tapez l’extension de balisage dans un fichier XAML, il est naturel de délimiter les valeurs des propriétés entre guillemets. Résistez à la tentation !

Voici le programme en cours d’exécution :

[![disposition relative à l’aide de contraintes](xaml-markup-extensions-images/relativelayout.png)](xaml-markup-extensions-images/relativelayout-large.png#lightbox)

## <a name="summary"></a>Résumé

Les extensions de balisage XAML illustrées ici important prennent en charge les fichiers XAML. Mais peut-être que l’extension de balisage XAML la plus précieuse est `Binding`, ce qui est abordé dans la partie suivante de cette série, [partie 4. Notions de base](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)de la liaison de données.

## <a name="related-links"></a>Liens connexes

- [XamlSamples](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)
- [Partie 1. Prise en main avec XAML](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [Partie 2. Syntaxe XAML essentielle](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [Partie 4. Principes fondamentaux de la liaison de données](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [Partie 5. De la liaison de données au MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
