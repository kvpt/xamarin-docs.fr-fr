---
title: Partie 3. Extensions de balisage XAML
description: Extensions de balisage XAML constituent une fonctionnalité importante dans XAML qui permettent d’être définie sur des objets ou des valeurs qui sont référencés indirectement à partir d’autres sources.
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F4A37564-B18B-42FF-B841-9A1949895AB6
author: davidbritch
ms.author: dabritch
ms.date: 03/27/2018
ms.openlocfilehash: 2417d6400faf80ad9b3b24a032046e7e867814f5
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68655490"
---
# <a name="part-3-xaml-markup-extensions"></a>Partie 3. Extensions de balisage XAML

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)

_Extensions de balisage XAML constituent une fonctionnalité importante dans XAML qui permettent d’être définie sur des objets ou des valeurs qui sont référencés indirectement à partir d’autres sources. Extensions de balisage XAML sont particulièrement importantes pour le partage d’objets et le référencement de constantes utilisées dans une application, mais ils trouver leur plus grande utilité dans les liaisons de données._

## <a name="xaml-markup-extensions"></a>Extensions de balisage XAML

En règle générale, vous utilisez XAML pour définir les propriétés d’un objet à des valeurs explicites, comme une chaîne, un nombre, un membre d’énumération ou une chaîne est convertie en une valeur dans les coulisses.

Parfois, cependant, propriétés doivent référencer à la place des valeurs définis à un endroit autre ou qui peut nécessiter un peu de traitement par le code lors de l’exécution. À ces fins, XAML *extensions de balisage* sont disponibles.

Ces extensions de balisage XAML ne sont pas des extensions de code XML. XAML est entièrement juridique XML. Ils sont appelés « extensions », car ils sont soutenus par le code dans les classes qui implémentent `IMarkupExtension`. Vous pouvez écrire vos propres extensions de balisage personnalisée.

Dans de nombreux cas, les extensions de balisage XAML sont instantanément reconnaissables dans les fichiers XAML parce qu’ils apparaissent en tant que paramètres d’attribut délimitées par des accolades : {et}, mais parfois les extensions de balisage apparaissent dans le balisage en tant qu’éléments conventionnels.

## <a name="shared-resources"></a>Ressources partagées

Certaines pages XAML contient plusieurs vues avec les mêmes valeurs de propriétés. Par exemple, de nombreux paramètres de la propriété pour ces `Button` objets sont les mêmes :

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

Dans XAML, une solution classique consiste à stocker ces valeurs ou des objets dans un *dictionnaire de ressources*. Le `VisualElement` classe définit une propriété nommée `Resources` de type `ResourceDictionary`, c'est-à-dire un dictionnaire avec des clés de type `string` et valeurs de type `object`. Vous pouvez placer des objets dans ce dictionnaire et ensuite les référencer à partir du balisage, tout en XAML.

Pour utiliser un dictionnaire de ressources sur une page, incluez une paire de `Resources` balises d’élément de propriété. Il est plus commode de placer ces éléments en haut de la page :

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

Il est également nécessaire d’inclure explicitement `ResourceDictionary` balises :

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

Maintenant objets et les valeurs de différents types peuvent être ajoutés pour le dictionnaire de ressources. Ces types doivent être instanciables. Ils ne peuvent pas être des classes abstraites, par exemple. Ces types doivent avoir également d’un constructeur sans paramètre public. Chaque élément requiert une clé de dictionnaire spécifiée avec le `x:Key` attribut. Exemple :

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

Ces deux éléments sont des valeurs du type structure `LayoutOptions`et chacun possède une clé unique et un ou deux propriétés définies. Dans le code et le balisage, il est beaucoup plus courant d’utiliser les champs statiques de `LayoutOptions`, mais il est plus pratique définir les propriétés.

Maintenant il est nécessaire de définir le `HorizontalOptions` et `VerticalOptions` propriétés de ces boutons pour ces ressources, et qui est effectué avec le `StaticResource` extension de balisage XAML :

```xaml
<Button Text="Do this!"
        HorizontalOptions="{StaticResource horzOptions}"
        VerticalOptions="{StaticResource vertOptions}"
        BorderWidth="3"
        Rotation="-15"
        TextColor="Red"
        FontSize="24" />
```

Le `StaticResource` extension de balisage est toujours délimitées par des accolades et inclut la clé de dictionnaire.

Le nom `StaticResource` particularité le distingue `DynamicResource`, lequel Xamarin.Forms prend également en charge. `DynamicResource` concerne les clés de dictionnaire associées aux valeurs qui peuvent changer pendant l’exécution, tandis que `StaticResource` accède aux éléments du dictionnaire qu’une fois lors de la construction des éléments sur la page.

Pour le `BorderWidth` propriété, il est nécessaire de stocker une valeur double dans le dictionnaire. XAML définit aisément des balises pour les types de données courants tels que `x:Double` et `x:Int32`:

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

Ces deux ressources peuvent être référencées dans la même façon que le `LayoutOptions` valeurs :

```xaml
<Button Text="Do this!"
        HorizontalOptions="{StaticResource horzOptions}"
        VerticalOptions="{StaticResource vertOptions}"
        BorderWidth="{StaticResource borderWidth}"
        Rotation="{StaticResource rotationAngle}"
        TextColor="Red"
        FontSize="24" />
```

Pour les ressources de type `Color`, vous pouvez utiliser des représentations sous forme de chaîne même, vous utilisez lorsque vous attribuez directement des attributs de ces types. Les convertisseurs de type sont appelées lorsque la ressource est créée. Voici une ressource de type `Color`:

```xaml
<Color x:Key="textColor">Red</Color>
```

Souvent, les programmes ensemble un `FontSize` propriété à un membre de la `NamedSize` énumération comme `Large`. Le `FontSizeConverter` fonctionne en arrière-plan pour le convertir en une valeur de dépend de la plateforme à l’aide de la classe la `Device.GetNamedSized` (méthode). Toutefois, lorsque vous définissez une ressource de la taille de police, il est plus judicieux d’utiliser une valeur numérique, illustrée ici comme un `x:Double` type :

```xaml
<x:Double x:Key="fontSize">24</x:Double>
```

Maintenant toutes les propriétés excepté `Text` sont définis par les paramètres de ressource :

```xaml
<Button Text="Do this!"
        HorizontalOptions="{StaticResource horzOptions}"
        VerticalOptions="{StaticResource vertOptions}"
        BorderWidth="{StaticResource borderWidth}"
        Rotation="{StaticResource rotationAngle}"
        TextColor="{StaticResource textColor}"
        FontSize="{StaticResource fontSize}" />
```

Il est également possible d’utiliser `OnPlatform` dans le dictionnaire de ressources pour définir des valeurs différentes pour les plateformes. Voici comment un `OnPlatform` objet peut faire partie du dictionnaire de ressources pour les couleurs de texte différente :

```xaml
<OnPlatform x:Key="textColor"
            x:TypeArguments="Color">
    <On Platform="iOS" Value="Red" />
    <On Platform="Android" Value="Aqua" />
    <On Platform="UWP" Value="#80FF80" />
</OnPlatform>
```

Notez que `OnPlatform` obtient à la fois un `x:Key` , car il est un objet dans le dictionnaire d’attributs et un `x:TypeArguments` , car c’est une classe générique d’attribut. Le `iOS`, `Android`, et `UWP` sont convertis en attributs `Color` valeurs lorsque l’objet est initialisé.

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

[![](xaml-markup-extensions-images/sharedresources.png "Contrôles de style")](xaml-markup-extensions-images/sharedresources-large.png#lightbox "des contrôles de style")

Bien qu’il est plus courant de définir la `Resources` collection en haut de la page, n’oubliez pas qui le `Resources` propriété est définie par `VisualElement`, et vous pouvez avoir `Resources` regroupements sur d’autres éléments sur la page. Par exemple, essayez d’ajouter un à le `StackLayout` dans cet exemple :

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

Vous découvrirez que la couleur du texte des boutons est désormais bleu. En fait, chaque fois que l’analyseur XAML rencontre un `StaticResource` extension de balisage, il recherche dans l’arborescence visuelle et utilise le premier `ResourceDictionary` il rencontre contenant cette clé.

Un des types courants d’objets stockés dans les dictionnaires de ressources est le Xamarin.Forms `Style`, qui définit une collection de paramètres de propriété. Les styles sont présentés dans l’article [Styles](~/xamarin-forms/user-interface/styles/index.md).

Les développeurs qui découvrent XAML se demandent parfois si elles peuvent placer un élément visuel comme `Label` ou `Button` dans un `ResourceDictionary`. Bien qu’il soit certainement possible, il n’y beaucoup de sens. L’objectif de la `ResourceDictionary` consiste à partager des objets. Un élément visuel ne peut pas être partagé. La même instance ne peut pas apparaître deux fois sur une seule page.

## <a name="the-xstatic-markup-extension"></a>L’Extension de balisage x : Static

En dépit des similarités de leurs noms, `x:Static` et `StaticResource` sont très différents. `StaticResource` Retourne un objet à partir d’un dictionnaire de ressources lors de la `x:Static` accède à l’une des opérations suivantes :

- un champ statique public
- une propriété statique publique
- un champ constant publique
- un membre d’énumération.

Le `StaticResource` extension de balisage est prise en charge par les implémentations XAML qui définissent un dictionnaire de ressources, tandis que `x:Static` fait partie intégrante de XAML, comme le `x` préfixe afin d’afficher.

Voici quelques exemples qui illustrent comment `x:Static` peut référencer explicitement les champs statiques et membres de l’énumération :

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="{x:Static LayoutOptions.Start}"
       HorizontalTextAlignment="{x:Static TextAlignment.Center}"
       TextColor="{x:Static Color.Aqua}" />
```

Jusqu’ici, ce n’est pas très impressionnant. Mais le `x:Static` extension de balisage peut également référencer des champs ou propriétés statiques à partir de votre propre code. Par exemple, voici une `AppConstants` classe qui contient des champs statiques que vous pouvez utiliser sur plusieurs pages dans une application :

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

Vous aurez besoin des déclarations d’espace de noms XML supplémentaires pour accéder aux autres classes. Chaque déclaration d’espace de noms XML supplémentaire définit un nouveau préfixe. Pour accéder aux classes locales à la bibliothèque .NET Standard d’applications partagé, tel que `AppConstants`, les programmeurs XAML utilisent souvent le préfixe `local`. La déclaration d’espace de noms doit indiquer le nom d’espace de noms CLR (Common Language Runtime), également connu sous l’espace de noms nom .NET, qui est le nom qui apparaît dans le langage C# `namespace` définition ou dans un `using` directive :

```csharp
xmlns:local="clr-namespace:XamlSamples"
```

Vous pouvez également définir des déclarations d’espace de noms XML pour les espaces de noms .NET dans n’importe quel assembly fait référence à la bibliothèque .NET Standard. Par exemple, voici une `sys` préfixe pour le .NET standard `System` espace de noms, qui se trouve dans le **mscorlib** assembly, ce qui signifiait qu’une seule fois « Common Runtime bibliothèque d’objets Microsoft », mais désormais signifie « multilingue Standard Objet Runtime bibliothèque commune. » Comme il s’agit d’un autre assembly, vous devez également spécifier le nom de l’assembly, dans ce cas **mscorlib**:

```csharp
xmlns:sys="clr-namespace:System;assembly=mscorlib"
```

Notez que le mot clé `clr-namespace` est suivi par un signe deux-points, puis le nom d’espace de noms de .NET, suivi par un point-virgule, le mot clé `assembly`, un signe égal et le nom de l’assembly.

Oui, un signe deux-points suit `clr-namespace` mais signe égal suit `assembly`. La syntaxe a été définie de cette façon délibérée: La plupart des déclarations d’espaces de noms XML référencent un URI qui commence `http`un nom de modèle d’URI tel que, qui est toujours suivi d’un signe deux-points. Le `clr-namespace` partie de cette chaîne est destiné à reproduire cette convention.

Les deux ces déclarations d’espace de noms sont incluses dans le **StaticConstantsPage** exemple. Notez que le `BoxView` dimensions sont définies pour `Math.PI` et `Math.E`, mais à l’échelle par un facteur de 100 :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples"
             xmlns:sys="clr-namespace:System;assembly=mscorlib"
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

La taille de la résultante `BoxView` par rapport à l’écran est dépendante de la plateforme :

 [![](xaml-markup-extensions-images/staticconstants.png "Contrôles à l’aide de x : Static, Extension de balisage")](xaml-markup-extensions-images/staticconstants-large.png#lightbox "contrôles à l’aide de x : Static, Extension de balisage")

## <a name="other-standard-markup-extensions"></a>Autres Extensions de balisage Standard

Plusieurs extensions de balisage sont intrinsèques à XAML et pris en charge dans les fichiers XAML de Xamarin.Forms. Certaines d'entre elles ne sont pas très souvent utilisés, mais sont essentielles lorsque vous en avez besoin :

-  Si une propriété a non - `null` valeur par défaut, mais vous souhaitez lui attribuer `null`, affectez-lui le `{x:Null}` extension de balisage.
-  Si une propriété est de type `Type`, vous pouvez l’affecter à un `Type` à l’aide de l’extension de balisage de l’objet `{x:Type someClass}`.
-  Vous pouvez définir des tableaux à l’aide de XAML le `x:Array` extension de balisage. Cette extension de balisage a un attribut obligatoire nommé `Type` qui indique le type des éléments dans le tableau.
- Le `Binding` extension de balisage est abordée dans [partie 4. Liaison des bases de données](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md).

## <a name="the-constraintexpression-markup-extension"></a>L’Extension de balisage de ConstraintExpression

Extensions de balisage peuvent avoir des propriétés, mais elles ne sont pas définies comme attributs XML. Dans une extension de balisage, les paramètres de propriété sont séparés par des virgules, et sans les guillemets apparaissent entre accolades.

Cela peut être illustré par l’extension de balisage Xamarin.Forms nommée `ConstraintExpression`, qui est utilisé avec la `RelativeLayout` classe. Vous pouvez spécifier l’emplacement ou la taille d’une vue enfant en tant que constante ou par rapport à un parent ou une autre vue nommée. La syntaxe de la `ConstraintExpression` permet de définir la position ou la taille d’une vue en utilisant un `Factor` fois une propriété d’une autre vue, ainsi qu’une `Constant`. Toute opération plus complexe que celle nécessite du code.

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

La leçon la plus importante que vous devez suivre à partir de cet exemple est la syntaxe de l’extension de balisage: Aucun guillemet ne doit apparaître entre les accolades d’une extension de balisage. Lorsque vous tapez l’extension de balisage dans un fichier XAML, il est naturel de délimiter les valeurs des propriétés entre guillemets. Résistez à la tentation !

Voici le programme en cours d’exécution :

[![](xaml-markup-extensions-images/relativelayout.png "Mise en page relative à l’aide de contraintes")](xaml-markup-extensions-images/relativelayout-large.png#lightbox "disposition relatif à l’aide de contraintes")

## <a name="summary"></a>Récapitulatif

Les extensions de balisage XAML illustrées ici important prennent en charge les fichiers XAML. Mais l’extension de balisage XAML plus précieuse est sans doute `Binding`, qui est abordé dans la partie suivante de cette série, [partie 4. Liaison des bases de données](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md).



## <a name="related-links"></a>Liens associés

- [XamlSamples](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)
- [Partie 1. Bien démarrer avec XAML](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [Partie 2. Syntaxe XAML essentielle](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [Partie 4. Notions de base sur la liaison de données](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [Partie 5. À partir de la liaison de données à MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
