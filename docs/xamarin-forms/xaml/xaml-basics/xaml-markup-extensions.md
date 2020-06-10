---
titre : «partie 3. Description des extensions de balisage XAML : les extensions de balisage XAML constituent une fonctionnalité importante du langage XAML qui permet de définir des propriétés sur des objets ou des valeurs qui sont référencés indirectement à partir d’autres sources.»
ms. Prod : xamarin ms. Technology : xamarin-Forms ms. AssetID : F4A37564-B18B-42FF-B841-9A1949895AB6 auteur : davidbritch ms. Author : dabritch ms. Date : 03/27/2018 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="part-3-xaml-markup-extensions"></a>Partie 3. Extensions de balisage XAML

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)

_Les extensions de balisage XAML constituent une fonctionnalité importante du langage XAML qui permet de définir des propriétés sur des objets ou des valeurs qui sont référencés indirectement à partir d’autres sources. Les extensions de balisage XAML sont particulièrement importantes pour le partage d’objets et le référencement des constantes utilisées dans une application, mais elles trouvent leur plus grand utilitaire dans les liaisons de données._

## <a name="xaml-markup-extensions"></a>Extensions de balisage XAML

En général, vous utilisez XAML pour définir les propriétés d’un objet sur des valeurs explicites, telles qu’une chaîne, un nombre, un membre de l’énumération ou une chaîne convertie en une valeur en arrière-plan.

Parfois, toutefois, les propriétés doivent référencer à la place des valeurs définies ailleurs, ou qui peuvent nécessiter un petit traitement par le code au moment de l’exécution. À ces fins, les *extensions de balisage* XAML sont disponibles.

Ces extensions de balisage XAML ne sont pas des extensions de XML. XAML est entièrement conforme à XML. Ils sont appelés « extensions », car ils sont sauvegardés par le code dans les classes qui implémentent `IMarkupExtension` . Vous pouvez écrire vos propres extensions de balisage personnalisées.

Dans de nombreux cas, les extensions de balisage XAML sont instantanément reconnaissables dans les fichiers XAML, car elles apparaissent en tant que paramètres d’attribut délimités par des accolades : {et}, mais parfois des extensions de balisage apparaissent dans le balisage en tant qu’éléments conventionnels.

## <a name="shared-resources"></a>Ressources partagées

Certaines pages XAML contiennent plusieurs vues avec des propriétés définies sur les mêmes valeurs. Par exemple, la plupart des paramètres de propriété de ces `Button` objets sont les mêmes :

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

Si l’une de ces propriétés doit être modifiée, vous préférerez peut-être effectuer la modification une seule fois au lieu de trois fois. S’il s’agissait de code, vous utiliseriez probablement des constantes et des objets statiques en lecture seule pour aider à maintenir une telle valeur cohérente et facile à modifier.

En XAML, une solution courante consiste à stocker des valeurs ou des objets de ce type dans un *dictionnaire de ressources*. La `VisualElement` classe définit une propriété nommée `Resources` de type `ResourceDictionary` , qui est un dictionnaire avec des clés de type `string` et des valeurs de type `object` . Vous pouvez placer des objets dans ce dictionnaire, puis les référencer dans le balisage, le tout en XAML.

Pour utiliser un dictionnaire de ressources sur une page, incluez une paire de `Resources` balises d’éléments de propriété. Il est plus pratique de les placer en haut de la page :

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

Il est également nécessaire d’inclure explicitement des `ResourceDictionary` Balises :

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

Désormais, les objets et les valeurs de différents types peuvent être ajoutés au dictionnaire de ressources. Ces types doivent être instanciables. Ils ne peuvent pas être des classes abstraites, par exemple. Ces types doivent également avoir un constructeur sans paramètre public. Chaque élément requiert une clé de dictionnaire spécifiée avec l' `x:Key` attribut. Par exemple :

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

Ces deux éléments sont des valeurs du type structure `LayoutOptions` et chacun a une clé unique et une ou deux propriétés définies. Dans le code et le balisage, il est bien plus courant d’utiliser les champs statiques de `LayoutOptions` , mais ici il est plus pratique de définir les propriétés.

Il est maintenant nécessaire de définir les `HorizontalOptions` `VerticalOptions` Propriétés et de ces boutons sur ces ressources, ce qui est fait avec l' `StaticResource` extension de balisage XAML :

```xaml
<Button Text="Do this!"
        HorizontalOptions="{StaticResource horzOptions}"
        VerticalOptions="{StaticResource vertOptions}"
        BorderWidth="3"
        Rotation="-15"
        TextColor="Red"
        FontSize="24" />
```

L' `StaticResource` extension de balisage est toujours délimitée par des accolades et comprend la clé de dictionnaire.

Le nom le `StaticResource` distingue de `DynamicResource` , qui Xamarin.Forms prend également en charge. `DynamicResource`concerne les clés de dictionnaire associées à des valeurs qui peuvent changer pendant l’exécution, tandis que `StaticResource` accède aux éléments du dictionnaire une seule fois lorsque les éléments de la page sont construits.

Pour la `BorderWidth` propriété, il est nécessaire de stocker un double dans le dictionnaire. XAML définit facilement des balises pour les types de données courants tels que `x:Double` et `x:Int32` :

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

Vous n’avez pas besoin de le placer sur trois lignes. Cette entrée de dictionnaire pour cet angle de rotation n’utilise qu’une seule ligne :

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

Ces deux ressources peuvent être référencées de la même façon que les `LayoutOptions` valeurs :

```xaml
<Button Text="Do this!"
        HorizontalOptions="{StaticResource horzOptions}"
        VerticalOptions="{StaticResource vertOptions}"
        BorderWidth="{StaticResource borderWidth}"
        Rotation="{StaticResource rotationAngle}"
        TextColor="Red"
        FontSize="24" />
```

Pour les ressources de type `Color` , vous pouvez utiliser les mêmes représentations sous forme de chaîne que celles que vous utilisez pour assigner directement des attributs de ces types. Les convertisseurs de type sont appelés lors de la création de la ressource. Voici une ressource de type `Color` :

```xaml
<Color x:Key="textColor">Red</Color>
```

Souvent, les programmes définissent une `FontSize` propriété en tant que membre de l' `NamedSize` énumération, par exemple `Large` . La `FontSizeConverter` classe fonctionne en arrière-plan pour la convertir en une valeur dépendante de la plateforme à l’aide de la `Device.GetNamedSized` méthode. Toutefois, lors de la définition d’une ressource de taille de police, il est plus judicieux d’utiliser une valeur numérique, indiquée ici comme un `x:Double` type :

```xaml
<x:Double x:Key="fontSize">24</x:Double>
```

Maintenant, toutes les propriétés `Text` sont définies par les paramètres de ressource :

```xaml
<Button Text="Do this!"
        HorizontalOptions="{StaticResource horzOptions}"
        VerticalOptions="{StaticResource vertOptions}"
        BorderWidth="{StaticResource borderWidth}"
        Rotation="{StaticResource rotationAngle}"
        TextColor="{StaticResource textColor}"
        FontSize="{StaticResource fontSize}" />
```

Il est également possible d’utiliser `OnPlatform` dans le dictionnaire de ressources pour définir différentes valeurs pour les plateformes. Voici comment un `OnPlatform` objet peut faire partie du dictionnaire de ressources pour différentes couleurs de texte :

```xaml
<OnPlatform x:Key="textColor"
            x:TypeArguments="Color">
    <On Platform="iOS" Value="Red" />
    <On Platform="Android" Value="Aqua" />
    <On Platform="UWP" Value="#80FF80" />
</OnPlatform>
```

Notez que `OnPlatform` Obtient `x:Key` à la fois un attribut, car il s’agit d’un objet dans le dictionnaire et d’un `x:TypeArguments` attribut, car il s’agit d’une classe générique. Les `iOS` `Android` attributs, et `UWP` sont convertis en `Color` valeurs lorsque l’objet est initialisé.

Voici le fichier XAML final complet avec trois boutons accédant à six valeurs partagées :

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

Les captures d’écran vérifient le style cohérent et le style dépendant de la plateforme :

[![Contrôles de style](xaml-markup-extensions-images/sharedresources.png)](xaml-markup-extensions-images/sharedresources-large.png#lightbox)

Bien qu’il soit plus courant de définir la `Resources` collection en haut de la page, gardez à l’esprit que la `Resources` propriété est définie par `VisualElement` , et que vous pouvez avoir des `Resources` collections sur d’autres éléments de la page. Par exemple, essayez d’en ajouter un au `StackLayout` dans cet exemple :

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

Vous découvrirez que la couleur de texte des boutons est désormais bleue. En fait, chaque fois que l’analyseur XAML rencontre une `StaticResource` extension de balisage, il recherche dans l’arborescence d’éléments visuels et utilise la première `ResourceDictionary` rencontre contenant cette clé.

L’un des types d’objets les plus courants stockés dans les dictionnaires de ressources est Xamarin.Forms `Style` , qui définit une collection de paramètres de propriété. Les styles sont présentés dans les [styles](~/xamarin-forms/user-interface/styles/index.md)d’article.

Parfois, les développeurs qui débutent avec XAML se demandent s’ils peuvent placer un élément visuel tel que `Label` ou `Button` dans un `ResourceDictionary` . Bien qu’il soit très possible, cela n’a pas de sens. L’objectif de `ResourceDictionary` est de partager des objets. Un élément visuel ne peut pas être partagé. La même instance ne peut pas apparaître deux fois sur une seule page.

## <a name="the-xstatic-markup-extension"></a>L’extension de balisage x :Static

En dépit des similitudes de leurs noms, `x:Static` et `StaticResource` sont très différents. `StaticResource`retourne un objet à partir d’un dictionnaire de ressources alors qu' `x:Static` il accède à l’un des éléments suivants :

- champ statique public
- une propriété statique publique
- champ de constante publique
- membre de l’énumération.

L' `StaticResource` extension de balisage est prise en charge par les implémentations XAML qui définissent un dictionnaire de ressources, tandis que `x:Static` est une partie intrinsèque de XAML, comme le montre le `x` préfixe.

Voici quelques exemples qui illustrent comment `x:Static` peut faire référence explicitement à des champs statiques et des membres de l’énumération :

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="{x:Static LayoutOptions.Start}"
       HorizontalTextAlignment="{x:Static TextAlignment.Center}"
       TextColor="{x:Static Color.Aqua}" />
```

Jusqu’à présent, ce n’est pas très impressionnant. Toutefois `x:Static` , l’extension de balisage peut également référencer des champs ou des propriétés statiques à partir de votre propre code. Par exemple, voici une `AppConstants` classe qui contient des champs statiques que vous pouvez utiliser sur plusieurs pages dans une application :

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

Pour référencer les champs statiques de cette classe dans le fichier XAML, vous avez besoin d’un moyen d’indiquer dans le fichier XAML où se trouve ce fichier. Pour ce faire, vous devez utiliser une déclaration d’espace de noms XML.

Rappelez-vous que les fichiers XAML créés dans le cadre du Xamarin.Forms modèle XAML standard contiennent deux déclarations d’espaces de noms XML : une pour accéder aux Xamarin.Forms classes et une autre pour référencer des balises et des attributs intrinsèques à XAML :

```csharp
xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
```

Vous aurez besoin de déclarations d’espace de noms XML supplémentaires pour accéder aux autres classes. Chaque déclaration d’espace de noms XML supplémentaire définit un nouveau préfixe. Pour accéder aux classes locales de la bibliothèque de .NET Standard d’applications partagées, par exemple `AppConstants` , les programmeurs XAML utilisent souvent le préfixe `local` . La déclaration d’espace de noms doit indiquer le nom de l’espace de noms CLR (Common Language Runtime), également appelé nom de l’espace de noms .NET, qui est le nom qui apparaît dans une `namespace` définition C# ou dans une `using` directive :

```csharp
xmlns:local="clr-namespace:XamlSamples"
```

Vous pouvez également définir des déclarations d’espaces de noms XML pour les espaces de noms .NET dans n’importe quel assembly auquel la bibliothèque .NET Standard fait référence. Par exemple, voici un `sys` préfixe pour l’espace de `System` noms .NET standard, qui se trouve dans l’assembly **netstandard** . Étant donné qu’il s’agit d’un autre assembly, vous devez également spécifier le nom de l’assembly, dans ce cas **netstandard**:

```csharp
xmlns:sys="clr-namespace:System;assembly=netstandard"
```

Notez que le mot clé `clr-namespace` est suivi d’un signe deux-points, puis du nom de l’espace de noms .net, suivi d’un point-virgule, du mot clé `assembly` , d’un signe égal et du nom de l’assembly.

Oui, un signe deux-points suit `clr-namespace` mais le signe égal suit `assembly` . La syntaxe a été définie de cette façon délibérée : la plupart des déclarations d’espaces de noms XML référencent un URI qui commence un nom de modèle d’URI tel que `http` , qui est toujours suivi d’un signe deux-points. La `clr-namespace` partie de cette chaîne est destinée à imiter cette Convention.

Ces deux déclarations d’espaces de noms sont incluses dans l’exemple **StaticConstantsPage** . Notez que les `BoxView` dimensions sont définies sur `Math.PI` et `Math.E` , mais mises à l’échelle par un facteur de 100 :

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

La taille de la résultante `BoxView` par rapport à l’écran dépend de la plateforme :

[![Contrôles utilisant l’extension de balisage x :Static](xaml-markup-extensions-images/staticconstants.png)](xaml-markup-extensions-images/staticconstants-large.png#lightbox)

## <a name="other-standard-markup-extensions"></a>Autres extensions de balisage standard

Plusieurs extensions de balisage sont intrinsèques à XAML et prises en charge dans les Xamarin.Forms fichiers XAML. Certains d’entre eux ne sont pas utilisés très souvent, mais sont essentiels lorsque vous en avez besoin :

- Si une propriété a une valeur qui n’est pas une `null` valeur par défaut, mais que vous souhaitez lui affecter `null` la valeur, affectez-lui la valeur de l' `{x:Null}` extension de balisage.
- Si une propriété est de type `Type` , vous pouvez l’assigner à un `Type` objet à l’aide de l’extension de balisage `{x:Type someClass}` .
- Vous pouvez définir des tableaux en XAML à l’aide de l' `x:Array` extension de balisage. Cette extension de balisage a un attribut obligatoire nommé `Type` qui indique le type des éléments dans le tableau.
- L' `Binding` extension de balisage est présentée dans la [partie 4. Notions de base](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)de la liaison de données.
- L' `RelativeSource` extension de balisage est présentée dans [liaisons relatives](~/xamarin-forms/app-fundamentals/data-binding/relative-bindings.md).

## <a name="the-constraintexpression-markup-extension"></a>Extension de balisage ConstraintExpression

Les extensions de balisage peuvent avoir des propriétés, mais elles ne sont pas définies comme des attributs XML. Dans une extension de balisage, les paramètres de propriété sont séparés par des virgules et aucun guillemet n’apparaît entre les accolades.

Cela peut être illustré avec l' Xamarin.Forms extension de balisage nommée `ConstraintExpression` , qui est utilisée avec la `RelativeLayout` classe. Vous pouvez spécifier l’emplacement ou la taille d’une vue enfant en tant que constante, ou par rapport à un parent ou à une autre vue nommée. La syntaxe de l `ConstraintExpression` 'vous permet de définir la position ou la taille d’une vue à l’aide d’une `Factor` fois une propriété d’une autre vue, plus un `Constant` . Tout ce qui est plus complexe que celui qui requiert du code.

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

La leçon la plus importante que vous devez prendre de cet exemple est la syntaxe de l’extension de balisage : aucun guillemet ne doit apparaître entre les accolades d’une extension de balisage. Lors de la saisie de l’extension de balisage dans un fichier XAML, il est naturel de placer les valeurs des propriétés entre guillemets. Résistez à la tentation !

Voici le programme en cours d’exécution :

[![Disposition relative utilisant des contraintes](xaml-markup-extensions-images/relativelayout.png)](xaml-markup-extensions-images/relativelayout-large.png#lightbox)

## <a name="summary"></a>Résumé

Les extensions de balisage XAML indiquées ici fournissent une prise en charge importante pour les fichiers XAML. Mais peut-être l’extension de balisage XAML la plus précieuse est `Binding` , ce qui est abordé dans la partie suivante de cette série, [partie 4. Notions de base](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)de la liaison de données.

## <a name="related-links"></a>Liens connexes

- [XamlSamples](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)
- [Partie 1. Prise en main avec XAML](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [Partie 2. Syntaxe XAML essentielle](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [Partie 4. Principes fondamentaux de la liaison de données](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [Partie 5. De la liaison de données au MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
