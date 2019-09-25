---
title: Le Xamarin.Forms FlexLayout
description: FlexLayout destiné à empilage ou une collection de vues enfants d’habillage.
ms.prod: xamarin
ms.assetid: 6A91EA70-268C-462C-AAAF-F8DA011403F8
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 05/07/2018
ms.openlocfilehash: 187befd88c115133a92aa90a711438e7754518d5
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2019
ms.locfileid: "68648803"
---
# <a name="the-xamarinforms-flexlayout"></a>Le Xamarin.Forms FlexLayout

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)

_FlexLayout destiné à empilage ou une collection de vues enfants d’habillage._

Xamarin.Forms [ `FlexLayout` ](xref:Xamarin.Forms.FlexLayout) est une nouveauté de la version 3.0 de Xamarin.Forms. Il est basé sur le code CSS [Module de disposition de zone Flexible](http://www.w3.org/TR/css-flexbox-1/), couramment appelé _flex disposition_ ou _flex-box_, appelée ainsi car elle inclut de nombreuses options flexibles pour organiser les enfants dans la disposition.

`FlexLayout` est similaire à la Xamarin.Forms [ `StackLayout` ](~/xamarin-forms/user-interface/layouts/stack-layout.md) dans la mesure où il peut réorganiser ses enfants horizontalement et verticalement dans une pile. Toutefois, le `FlexLayout` est également capable de wrapping ses enfants si elles sont trop nombreuses pour tenir dans une seule ligne ou colonne, et dispose également de nombreuses options pour l’orientation, d’alignement et de s’adapter à différentes tailles d’écran.

`FlexLayout` dérive de [ `Layout<View>` ](xref:Xamarin.Forms.Layout`1) et hérite d’un [ `Children` ](xref:Xamarin.Forms.Layout`1.Children) propriété de type `IList<View>`.

`FlexLayout` définit six des propriétés publiques peut être liées et cinq propriétés jointes pouvant être liées qui affectent la taille, l’orientation et l’alignement de ses éléments enfants. (Si vous n’êtes pas familiarisé avec les propriétés pouvant être liées jointes, consultez l’article  **[propriétés jointes](~/xamarin-forms/xaml/attached-properties.md)** .) Ces propriétés sont décrites en détail dans les sections ci-dessous sur **[les propriétés pouvant être liées en détail](#bindable-properties)** et  **[les propriétés jointes pouvant être liées en détail](#attached-properties)** . Toutefois, cet article commence par une section sur certains **[scénarios d’utilisation courants](#common-scenarios)** de `FlexLayout` qui décrit un grand nombre de ces propriétés de manière plus informelle. Vers la fin de l’article, vous verrez comment combiner `FlexLayout` avec [feuilles de style CSS](~/xamarin-forms/user-interface/styles/css/index.md).

<a name="common-scenarios" />

## <a name="common-usage-scenarios"></a>Scénarios d’utilisation courants

Le **[FlexLayoutDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** exemple de programme contienne plusieurs pages qui illustrent certaines utilisations courantes de `FlexLayout` et vous permet de faire des essais avec ses propriétés.

### <a name="using-flexlayout-for-a-simple-stack"></a>À l’aide de FlexLayout pour une pile simple

Le **pile Simple** page montre comment `FlexLayout` pouvez remplacer par un `StackLayout` , mais avec un balisage plus simple. Tous les éléments de cet exemple sont défini dans la page XAML. Le `FlexLayout` contient quatre enfants :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:FlexLayoutDemos"
             x:Class="FlexLayoutDemos.SimpleStackPage"
             Title="Simple Stack">

    <FlexLayout Direction="Column"
                AlignItems="Center"
                JustifyContent="SpaceEvenly">

        <Label Text="FlexLayout in Action"
               FontSize="Large" />

        <Image Source="{local:ImageResource FlexLayoutDemos.Images.SeatedMonkey.jpg}" />

        <Button Text="Do-Nothing Button" />

        <Label Text="Another Label" />
    </FlexLayout>
</ContentPage>
```

Voici la page en cours d’exécution sur iOS, Android et la plateforme Windows universelle :

[![Page de la pile Simple](flex-layout-images/SimpleStack.png "Simple Page de la pile")](flex-layout-images/SimpleStack-Large.png#lightbox)

Trois propriétés de `FlexLayout` figurent dans le **SimpleStackPage.xaml** fichier :

- Le [ `Direction` ](xref:Xamarin.Forms.FlexLayout.Direction) propriété est définie sur une valeur de la [ `FlexDirection` ](xref:Xamarin.Forms.FlexDirection) énumération. La valeur par défaut est `Row`. Définissant la propriété sur `Column` entraîne les enfants de le `FlexLayout` à être réorganisées dans une colonne unique d’éléments.

    Lorsque les éléments dans un `FlexLayout` sont réorganisées dans une colonne, le `FlexLayout` est considéré comme un vertical _axe principal_ et un horizontal _croisé axe_.

- Le [ `AlignItems` ](xref:Xamarin.Forms.FlexLayout.AlignItems) propriété est de type [ `FlexAlignItems` ](xref:Xamarin.Forms.FlexAlignItems) et spécifie la façon dont les éléments sont alignés sur l’axe transversal. Le `Center` option provoque de chaque élément est centrée horizontalement.

    Si vous utilisiez un `StackLayout` au lieu d’un `FlexLayout` pour cette tâche, vous serez centrer tous les éléments en affectant la `HorizontalOptions` propriété de chaque élément à `Center`. Le `HorizontalOptions` propriété ne fonctionne pas pour les enfants d’un `FlexLayout`, mais le seul `AlignItems` propriété accomplit le même objectif. Si vous avez besoin, vous pouvez utiliser la `AlignSelf` attaché une propriété pouvant être liée à substituer le `AlignItems` propriété pour des éléments individuels :

    ```xaml
    <Label Text="FlexLayout in Action"
           FontSize="Large"
           FlexLayout.AlignSelf="Start" />
    ```

    Avec cette modification, celle-ci `Label` est positionné sur le bord gauche de la `FlexLayout` lorsque l’ordre de lecture est gauche à droite.

- Le [ `JustifyContent` ](xref:Xamarin.Forms.FlexLayout.JustifyContent) propriété est de type [ `FlexJustify` ](xref:Xamarin.Forms.FlexJustify)et spécifie la façon dont les éléments sont organisés sur l’axe principal. Le `SpaceEvenly` option alloue tout espace vertical restes équitablement entre tous les éléments et au-dessus du premier élément et en dessous du dernier élément.

    Si vous utilisiez un `StackLayout`, vous devez affecter la `VerticalOptions` propriété de chaque élément à `CenterAndExpand` pour obtenir un résultat similaire. Mais la `CenterAndExpand` option alloue deux fois plus d’espace entre chaque élément qu’avant le premier élément et après le dernier élément. Vous pouvez imiter le `CenterAndExpand` option de `VerticalOptions` en définissant le `JustifyContent` propriété du `FlexLayout` à `SpaceAround`.

Ces `FlexLayout` propriétés sont abordées plus en détail dans la section **[les propriétés pouvant être liées en détail](#bindable-properties)** ci-dessous.

### <a name="using-flexlayout-for-wrapping-items"></a>À l’aide de FlexLayout pour l’encapsulation d’éléments

Le **Photo encapsulant** page de la **[FlexLayoutDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** exemple illustre comment `FlexLayout` peut encapsuler ses enfants à d’autres lignes ou colonnes. Le fichier XAML instancie le `FlexLayout` et affecte les deux propriétés de celui-ci :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="FlexLayoutDemos.PhotoWrappingPage"
             Title="Photo Wrapping">
    <Grid>
        <ScrollView>
            <FlexLayout x:Name="flexLayout"
                        Wrap="Wrap"
                        JustifyContent="SpaceAround" />
        </ScrollView>

        <ActivityIndicator x:Name="activityIndicator"
                           IsRunning="True"
                           VerticalOptions="Center" />
    </Grid>
</ContentPage>
```

Le `Direction` propriété de ce `FlexLayout` n’est pas définie, elle a la valeur par défaut `Row`, ce qui signifie que les enfants sont organisées en lignes et l’axe principal est horizontal.

Le [ `Wrap` ](xref:Xamarin.Forms.FlexLayout.Wrap) propriété est de type énumération [ `FlexWrap` ](xref:Xamarin.Forms.FlexWrap). S’il existe trop d’éléments pour tenir sur une ligne, ce paramètre de propriété provoque ensuite les éléments à inclure dans un wrapper à la ligne suivante.

Notez que le `FlexLayout` est un enfant d’un `ScrollView`. S’il existe trop de lignes pour s’ajuster à la page, puis le `ScrollView` a une valeur par défaut `Orientation` propriété du `Vertical` et permet le défilement vertical.

Le `JustifyContent` propriété alloue de l’espace restante sur l’axe principal (l’axe horizontal) afin que chaque élément est entourée par la même quantité d’espace vide.

Le fichier code-behind accède à une collection d’exemples de photos et les ajoute à la `Children` collection de la `FlexLayout`:

```csharp
public partial class PhotoWrappingPage : ContentPage
{
    // Class for deserializing JSON list of sample bitmaps
    [DataContract]
    class ImageList
    {
        [DataMember(Name = "photos")]
        public List<string> Photos = null;
    }

    public PhotoWrappingPage ()
    {
        InitializeComponent ();

        LoadBitmapCollection();
    }

    async void LoadBitmapCollection()
    {
        using (WebClient webClient = new WebClient())
        {
            try
            {
                // Download the list of stock photos
                Uri uri = new Uri("https://raw.githubusercontent.com/xamarin/docs-archive/master/Images/stock/small/stock.json");
                byte[] data = await webClient.DownloadDataTaskAsync(uri);

                // Convert to a Stream object
                using (Stream stream = new MemoryStream(data))
                {
                    // Deserialize the JSON into an ImageList object
                    var jsonSerializer = new DataContractJsonSerializer(typeof(ImageList));
                    ImageList imageList = (ImageList)jsonSerializer.ReadObject(stream);

                    // Create an Image object for each bitmap
                    foreach (string filepath in imageList.Photos)
                    {
                        Image image = new Image
                        {
                            Source = ImageSource.FromUri(new Uri(filepath))
                        };
                        flexLayout.Children.Add(image);
                    }
                }
            }
            catch
            {
                flexLayout.Children.Add(new Label
                {
                    Text = "Cannot access list of bitmap files"
                });
            }
        }

        activityIndicator.IsRunning = false;
        activityIndicator.IsVisible = false;
    }
}
```

Voici le programme en cours d’exécution, progressivement défilé de haut en bas :

[![La Page d’habillage de Photo](flex-layout-images/PhotoWrapping.png "la Page d’habillage de Photo")](flex-layout-images/PhotoWrapping-Large.png#lightbox)

### <a name="page-layout-with-flexlayout"></a>Mise en page avec FlexLayout

Il existe une disposition standard dans la conception web appelée le [ _Salut_ ](https://en.wikipedia.org/wiki/Holy_grail_(web_design)) , car c’est un format de disposition est très souhaitable, mais il est souvent difficile à réaliser avec la perfection. La disposition se compose d’un en-tête en haut de la page et un pied de page en bas, les deux étendre sur toute la largeur de la page. Occupée par le centre de la page est le contenu principal, mais souvent avec un menu en colonnes à gauche les contenu et les informations supplémentaires (parfois appelé un _côté_ zone) à droite. [Section 5.4.1 de la spécification de mise en page de boîte Flexible CSS](http://www.w3.org/TR/css-flexbox-1/#order-accessibility) décrit la façon dont la disposition de Salut peut être réalisée avec une zone de flex.

Le **Salut disposition** page de la **[FlexLayoutDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** exemple illustre une implémentation simple de cette disposition à l’aide d’un `FlexLayout` imbriqué dans un autre. Étant donné que cette page est conçue pour un téléphone en mode portrait, les zones à gauche et droite de la zone de contenu sont uniquement 50 pixels de large :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="FlexLayoutDemos.HolyGrailLayoutPage"
             Title="Holy Grail Layout">

    <FlexLayout Direction="Column">

        <!-- Header -->
        <Label Text="HEADER"
               FontSize="Large"
               BackgroundColor="Aqua"
               HorizontalTextAlignment="Center" />

        <!-- Body -->
        <FlexLayout FlexLayout.Grow="1">

            <!-- Content -->
            <Label Text="CONTENT"
                   FontSize="Large"
                   BackgroundColor="Gray"
                   HorizontalTextAlignment="Center"
                   VerticalTextAlignment="Center"
                   FlexLayout.Grow="1" />

            <!-- Navigation items-->
            <BoxView FlexLayout.Basis="50"
                     FlexLayout.Order="-1"
                     Color="Blue" />

            <!-- Aside items -->
            <BoxView FlexLayout.Basis="50"
                     Color="Green" />

        </FlexLayout>

        <!-- Footer -->
        <Label Text="FOOTER"
               FontSize="Large"
               BackgroundColor="Pink"
               HorizontalTextAlignment="Center" />
    </FlexLayout>
</ContentPage>
```

Ici, il s’exécute :

[![La Page de disposition Salut](flex-layout-images/HolyGrailLayout.png "la Page de disposition Salut")](flex-layout-images/HolyGrailLayout-Large.png#lightbox)

Les zones aside et de navigation sont restitués avec un `BoxView` sur la gauche et droite.

Le premier `FlexLayout` dans le XAML fichier a un axe principal et contient les trois enfants sont réorganisés dans une colonne. Il s’agit de l’en-tête, le corps de la page et le pied de page. Imbriqué `FlexLayout` a un axe principal horizontal avec trois enfants organisés dans une ligne.

Trois propriétés pouvant être liées attachées sont illustrées dans ce programme :

- Le `Order` propriété pouvant être liée jointe est définie sur le premier `BoxView`. Cette propriété est un entier dont la valeur par défaut 0. Vous pouvez utiliser cette propriété pour modifier l’ordre de mise en page. En général, les développeurs préfèrent le contenu de la page s’affiche dans le balisage avant les éléments de navigation et mettre de côté les éléments. Définition de la `Order` propriété sur le premier `BoxView` sur une valeur inférieure à ses autres frères fait apparaître comme premier élément dans la ligne. De même, vous pouvez vous assurer qu’un élément apparaît en dernier en définissant le `Order` propriété une valeur supérieure à ses frères.

- Le `Basis` propriété pouvant être liée jointe est définie sur les deux `BoxView` éléments afin de leur donner une largeur de 50 pixels. Cette propriété est de type `FlexBasis`, une structure qui définit une propriété statique de type `FlexBasis` nommé `Auto`, qui est la valeur par défaut. Vous pouvez utiliser `Basis` pour spécifier une taille en pixels ou un pourcentage qui indique la quantité d’espace, l’élément occupe sur l’axe principal. Elle est appelée un _base_ car elle spécifie une taille de l’élément qui est la base de mise en page toutes les.

- Le `Grow` propriété est définie sur imbriqué `Layout` puis, dans le `Label` enfants représentant le contenu. Cette propriété est de type `float` et a la valeur par défaut 0. Lorsque la valeur est une valeur positive, tout l’espace restant sur l’axe principal est allouée à cet élément et à frères à l’aide des valeurs positives de `Grow`. L’espace est alloué proportionnellement aux valeurs, un peu comme la spécification en étoile dans un `Grid`.

    La première `Grow` propriété jointe est définie sur imbriqué `FlexLayout`, qui indique que ce `FlexLayout` consiste à occuper tout l’espace vertical inutilisé dans externe `FlexLayout`. La seconde `Grow` propriété jointe est définie sur le `Label` qui représente le contenu, indiquant que ce contenu est d’occuper tout l’espace horizontal inutilisé dans interne `FlexLayout`.

    Il est également un similaire `Shrink` attaché de propriété pouvant être liée que vous pouvez utiliser lorsque la taille d’enfants dépasse la taille de la `FlexLayout` mais habillage n’est pas souhaité.

### <a name="catalog-items-with-flexlayout"></a>Éléments du catalogue avec FlexLayout

Le **éléments de catalogue** page dans le **[FlexLayoutDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** exemple est similaire à [exemple 1 dans la version 1.1 de Section de la spécification de la zone de disposition flexible CSS](http://www.w3.org/TR/css-flexbox-1/#overview), à ceci près qu’il affiche une série de défilement horizontale d’images et les descriptions de trois singes :

[![Page des éléments de catalogue](flex-layout-images/CatalogItems.png "Page des éléments de catalogue")](flex-layout-images/CatalogItems-Large.png#lightbox)

Chacun des trois singes est un `FlexLayout` contenues dans un `Frame` donné une hauteur explicite et la largeur, et qui est également un enfant d’une plus grande `FlexLayout`. Dans ce fichier XAML, la plupart des propriétés de la `FlexLayout` enfants sont spécifiés dans les styles, tous sauf un d’eux est un style implicite :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:FlexLayoutDemos"
             x:Class="FlexLayoutDemos.CatalogItemsPage"
             Title="Catalog Items">
    <ContentPage.Resources>
        <Style TargetType="Frame">
            <Setter Property="BackgroundColor" Value="LightYellow" />
            <Setter Property="BorderColor" Value="Blue" />
            <Setter Property="Margin" Value="10" />
            <Setter Property="CornerRadius" Value="15" />
        </Style>

        <Style TargetType="Label">
            <Setter Property="Margin" Value="0, 4" />
        </Style>

        <Style x:Key="headerLabel" TargetType="Label">
            <Setter Property="Margin" Value="0, 8" />
            <Setter Property="FontSize" Value="Large" />
            <Setter Property="TextColor" Value="Blue" />
        </Style>

        <Style TargetType="Image">
            <Setter Property="FlexLayout.Order" Value="-1" />
            <Setter Property="FlexLayout.AlignSelf" Value="Center" />
        </Style>

        <Style TargetType="Button">
            <Setter Property="Text" Value="LEARN MORE" />
            <Setter Property="FontSize" Value="Large" />
            <Setter Property="TextColor" Value="White" />
            <Setter Property="BackgroundColor" Value="Green" />
            <Setter Property="BorderRadius" Value="20" />
        </Style>
    </ContentPage.Resources>

    <ScrollView Orientation="Both">
        <FlexLayout>
            <Frame WidthRequest="300"
                   HeightRequest="480">

                <FlexLayout Direction="Column">
                    <Label Text="Seated Monkey"
                           Style="{StaticResource headerLabel}" />
                    <Label Text="This monkey is laid back and relaxed, and likes to watch the world go by." />
                    <Label Text="  &#x2022; Doesn't make a lot of noise" />
                    <Label Text="  &#x2022; Often smiles mysteriously" />
                    <Label Text="  &#x2022; Sleeps sitting up" />
                    <Image Source="{local:ImageResource FlexLayoutDemos.Images.SeatedMonkey.jpg}"
                           WidthRequest="180"
                           HeightRequest="180" />
                    <Label FlexLayout.Grow="1" />
                    <Button />
                </FlexLayout>
            </Frame>

            <Frame WidthRequest="300"
                   HeightRequest="480">

                <FlexLayout Direction="Column">
                    <Label Text="Banana Monkey"
                           Style="{StaticResource headerLabel}" />
                    <Label Text="Watch this monkey eat a giant banana." />
                    <Label Text="  &#x2022; More fun than a barrel of monkeys" />
                    <Label Text="  &#x2022; Banana not included" />
                    <Image Source="{local:ImageResource FlexLayoutDemos.Images.Banana.jpg}"
                           WidthRequest="240"
                           HeightRequest="180" />
                    <Label FlexLayout.Grow="1" />
                    <Button />
                </FlexLayout>
            </Frame>

            <Frame WidthRequest="300"
                   HeightRequest="480">

                <FlexLayout Direction="Column">
                    <Label Text="Face-Palm Monkey"
                           Style="{StaticResource headerLabel}" />
                    <Label Text="This monkey reacts appropriately to ridiculous assertions and actions." />
                    <Label Text="  &#x2022; Cynical but not unfriendly" />
                    <Label Text="  &#x2022; Seven varieties of grimaces" />
                    <Label Text="  &#x2022; Doesn't laugh at your jokes" />
                    <Image Source="{local:ImageResource FlexLayoutDemos.Images.FacePalm.jpg}"
                           WidthRequest="180"
                           HeightRequest="180" />
                    <Label FlexLayout.Grow="1" />
                    <Button />
                </FlexLayout>
            </Frame>
        </FlexLayout>
    </ScrollView>
</ContentPage>
```

Le style implicit pour la `Image` inclut les paramètres des deux propriétés pouvant être liées attachées de `Flexlayout`:

```xaml
<Style TargetType="Image">
    <Setter Property="FlexLayout.Order" Value="-1" />
    <Setter Property="FlexLayout.AlignSelf" Value="Center" />
</Style>
```

Le `Order` paramètre &ndash;causes 1 le `Image` élément à afficher en premier dans chacune des imbriqué `FlexLayout` vues quelle que soit sa position dans la collection d’enfants. Le `AlignSelf` propriété du `Center` provoque la `Image` sera centré dans la `FlexLayout`. Ce paramètre remplace le paramètre de la `AlignItems` propriété, qui a comme valeur par défaut de `Stretch`, ce qui signifie qui les `Label` et `Button` enfants sont étirés pour toute la largeur de la `FlexLayout`.

Dans chacun des trois `FlexLayout` consulte, une valeur vide `Label` précède le `Button`, mais il a un `Grow` paramètre 1. Cela signifie que tout l’espace vertical est allouée à ce champ vide `Label`, ce qui pousse efficacement la `Button` vers le bas.

<a name="bindable-properties" />

## <a name="the-bindable-properties-in-detail"></a>Les propriétés pouvant être liées en détail

Maintenant que vous avez vu certaines applications courantes de `FlexLayout`, les propriétés de `FlexLayout` peuvent être examinés plus en détail.
`FlexLayout` définit six propriétés pouvant être liées que vous définissez sur le `FlexLayout` lui-même, soit dans le code ou XAML, l’alignement et l’orientation du contrôle. (Une de ces propriétés, [ `Position` ](xref:Xamarin.Forms.FlexLayout.Position), n’est pas couverte dans cet article.)

Vous pouvez expérimenter avec cinq restant des propriétés pouvant être liées à l’aide de la **faire des essais** page de la **[FlexLayoutDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** exemple. Cette page permet d’ajouter ou supprimer des enfants à partir d’un `FlexLayout` et pour définir des combinaisons des cinq propriétés pouvant être liées. Tous les enfants de la `FlexLayout` sont `Label` vues de différentes couleurs et tailles, avec le `Text` propriété définie sur un nombre correspondant à sa position dans le `Children` collection.

Lorsque le programme démarre, cinq `Picker` vues affichent les valeurs par défaut de ces cinq `FlexLayout` propriétés. Le `FlexLayout` vers le bas de l’écran contient trois enfants :

[![La page de l’expérience : Page]d'(flex-layout-images/ExperimentDefault.png "expérimentation par défaut-par défaut")](flex-layout-images/ExperimentDefault-Large.png#lightbox)

Chacun de la `Label` vues a un arrière-plan gris qui affiche l’espace alloué à cette `Label` au sein de la `FlexLayout`. L’arrière-plan de la `FlexLayout` lui-même est bleu Alice. Il occupe la zone entière en bas de la page à l’exception d’une petite marge à gauche et à droite.

<a name="direction" />

### <a name="the-direction-property"></a>La propriété Direction

Le [ `Direction` ](xref:Xamarin.Forms.FlexLayout.Direction) propriété est de type [ `FlexDirection` ](xref:Xamarin.Forms.FlexDirection), une énumération avec quatre membres :

- `Column`
- `ColumnReverse` (ou « colonne-reverse » dans XAML)
- `Row`, la valeur par défaut
- `RowReverse` (ou « ligne-reverse » dans XAML)

Dans XAML, vous pouvez spécifier la valeur de cette propriété en utilisant les noms de membre d’énumération en minuscules, majuscules, ou casse mixte, ou vous pouvez utiliser deux chaînes supplémentaires indiqués entre parenthèses sont les mêmes que les indicateurs CSS. (Les chaînes « colonne-reverse » et « ligne-reverse » sont définies dans le [ `FlexDirectionTypeConverter` ](xref:Xamarin.Forms.FlexDirectionTypeConverter) classe utilisée par l’analyseur XAML.)

Voici le **expérience** page indiquant (de gauche à droite), le `Row` direction, `Column` direction, et `ColumnReverse` direction :

[![La page de l’expérience : Direction]de la direction de(flex-layout-images/ExperimentDirection.png "la page d’expérimentation")](flex-layout-images/ExperimentDirection-Large.png#lightbox)

Notez que pour la `Reverse` options, les éléments démarrent au droit ou inférieur.

<a name="wrap" />

### <a name="the-wrap-property"></a>La propriété de type Wrap

Le [ `Wrap` ](xref:Xamarin.Forms.FlexLayout.Wrap) propriété est de type [ `FlexWrap` ](xref:Xamarin.Forms.FlexWrap), une énumération avec trois membres :

- `NoWrap`, la valeur par défaut
- `Wrap`
- `Reverse` (ou « de type wrap-reverse » dans XAML)

De gauche à droite, ces écrans montrent le `NoWrap`, `Wrap` et `Reverse` options pour les enfants de 12 :

[![La page de l’expérience : Encapsuler](flex-layout-images/ExperimentWrap.png "la page d’expérimentation")](flex-layout-images/ExperimentWrap-Large.png#lightbox)

Lorsque le `Wrap` propriété est définie sur `NoWrap` l’axe principal est limitée (comme dans ce programme) et l’axe principal n’est pas large ou assez haut pour tous les enfants, le `FlexLayout` tente de créer les éléments plus petits, comme la capture d’écran d’iOS montre. Vous pouvez contrôler le shrinkness des éléments de la [ `Shrink` ](#shrink) attaché de propriété pouvant être liée.

<a name="justify-content" />

### <a name="the-justifycontent-property"></a>La propriété JustifyContent

Le [ `JustifyContent` ](xref:Xamarin.Forms.FlexLayout.JustifyContent) propriété est de type [ `FlexJustify` ](xref:Xamarin.Forms.FlexJustify), une énumération avec les six membres :

- `Start` (ou « flex-start » dans XAML), la valeur par défaut
- `Center`
- `End` (ou « flex-end » dans XAML)
- `SpaceBetween` (ou « espace entre « dans XAML)
- `SpaceAround` (ou « espace around » dans XAML)
- `SpaceEvenly`

Cette propriété spécifie la façon dont les éléments sont espacées sur l’axe principal, qui est l’axe horizontal dans cet exemple :

[![La page de l’expérience : Justifier le]contenu(flex-layout-images/ExperimentJustifyContent.png "de la page d’expérimentation-justifier le contenu")](flex-layout-images/ExperimentJustifyContent-Large.png#lightbox)

Dans tous les trois captures d’écran, le `Wrap` propriété est définie sur `Wrap`. Le `Start` par défaut est indiqué dans la capture d’écran précédente Android. La capture d’écran iOS ici montre la `Center` option : tous les éléments sont déplacés vers le centre. Les trois autres options commence par le mot `Space` allouer l’espace supplémentaire ne pas occupée par les éléments. `SpaceBetween` alloue de l’espace de manière égale entre les éléments ; `SpaceAround` puts égal espace autour de chaque élément, tandis que `SpaceEvenly` puts égal espace entre chaque élément et avant le premier élément et après le dernier élément sur la ligne.

<a name="align-items" />

### <a name="the-alignitems-property"></a>La propriété AlignItems

Le [ `AlignItems` ](xref:Xamarin.Forms.FlexLayout.AlignItems) propriété est de type [ `FlexAlignItems` ](xref:Xamarin.Forms.FlexAlignItems), une énumération avec quatre membres :

- `Stretch`, la valeur par défaut
- `Center`
- `Start` (ou « flex-start » dans XAML)
- `End` (ou « flex-end » dans XAML)

Il s’agit d’une des deux propriétés (l’autre étant [ `AlignContent` ](#align-content)) qui indique la façon dont les enfants sont alignés sur l’axe transversal. Dans chaque ligne, les enfants sont étirés (comme indiqué dans la capture d’écran précédente) ou alignés sur le début, le centre ou la fin de chaque élément, comme indiqué dans les captures d’écran suivantes trois :

[![La page de l’expérience : Aligner les]éléments(flex-layout-images/ExperimentAlignItems.png "sur la page d’expérience-aligner les éléments")](flex-layout-images/ExperimentAlignItems-Large.png#lightbox)

Dans la capture d’écran iOS, les parties supérieures de tous les enfants sont alignés. Dans les captures d’écran Android, les éléments sont centrés verticalement en fonction de l’enfant plus haut. Dans la capture d’écran UWP, le bas de tous les éléments sont aligné.

Pour n’importe quel élément individuel, le `AlignItems` paramètre peut être remplacé par le [ `AlignSelf` ](#align-self) attaché de propriété pouvant être liée.

<a name="align-content" />

### <a name="the-aligncontent-property"></a>La propriété AlignContent

Le [ `AlignContent` ](xref:Xamarin.Forms.FlexLayout.AlignContent) propriété est de type [ `FlexAlignContent` ](xref:Xamarin.Forms.FlexAlignContent), une énumération avec sept membres :

- `Stretch`, la valeur par défaut
- `Center`
- `Start` (ou « flex-start » dans XAML)
- `End` (ou « flex-end » dans XAML)
- `SpaceBetween` (ou « espace entre « dans XAML)
- `SpaceAround` (ou « espace around » dans XAML)
- `SpaceEvenly`

Comme `AlignItems`, le `AlignContent` propriété également aligne enfants sur l’axe transversal, mais affecte des lignes entières ou des colonnes :

[![La page de l’expérience : Aligner le]contenu(flex-layout-images/ExperimentAlignContent.png "sur la page d’expérience-aligner le contenu")](flex-layout-images/ExperimentAlignContent-Large.png#lightbox)

Dans la capture d’écran iOS, les deux lignes figurent en haut ; dans la capture d’écran Android, ils sont dans le centre ; et dans la capture d’écran UWP ils en bas. Les lignes peuvent également être espacés de différentes manières :

[![La page de l’expérience :  Aligner le contenu]2(flex-layout-images/ExperimentAlignContent2.png "sur la page expérience-aligner le contenu 2")](flex-layout-images/ExperimentAlignContent2-Large.png#lightbox)

Le `AlignContent` n’a aucun effet lorsqu’il n'existe qu’une seule ligne ou colonne.

<a name="attached-properties" />

## <a name="the-attached-bindable-properties-in-detail"></a>Les propriétés jointes pouvant être liées en détail

`FlexLayout` définit les cinq propriétés pouvant être liées attachées. Ces propriétés sont définies sur les enfants de la `FlexLayout` et se rapportent uniquement à cet enfant particulier.

<a name="align-self" />

### <a name="the-alignself-property"></a>La propriété AlignSelf

Le [ `AlignSelf` ](xref:Xamarin.Forms.FlexLayout.AlignSelfProperty) propriété pouvant être liée jointe est de type [ `FlexAlignSelf` ](xref:Xamarin.Forms.FlexAlignContent), une énumération avec cinq membres :

- `Auto`, la valeur par défaut
- `Stretch`
- `Center`
- `Start` (ou « flex-start » dans XAML)
- `End` (ou « flex-end » dans XAML)

Pour les enfants individuels de la `FlexLayout`, cette propriété de configuration des remplacements le [ `AlignItems` ](#align-items) propriété définie sur la `FlexLayout` lui-même. Le paramètre par défaut `Auto` signifie que le `AlignItems` paramètre.

Pour un `Label` élément nommé `label` (ou exemple), vous pouvez définir le `AlignSelf` propriété dans le code comme suit :

```csharp
FlexLayout.SetAlignSelf(label, FlexAlignSelf.Center);
```

Notez qu’il n’existe aucune référence à la `FlexLayout` parent de la `Label`. Dans XAML, vous définissez la propriété comme suit :

```xaml
<Label ... FlexLayout.AlignSelf="Center" ... />
```

### <a name="the-order-property"></a>La propriété Order

Le [ `Order` ](xref:Xamarin.Forms.FlexLayout.OrderProperty) propriété est de type `int`. La valeur par défaut est 0.

Le `Order` propriété vous permet de modifier l’ordre que les enfants de la `FlexLayout` sont organisés. En règle générale, les enfants d’un `FlexLayout` sont organisés est le même ordre d’apparition dans le `Children` collection. Vous pouvez remplacer cet ordre en définissant le `Order` attaché de propriété pouvant être liée à une valeur entière non nulle sur un ou plusieurs enfants. Le `FlexLayout` réorganise ses enfants en fonction du paramètre de la `Order` propriété sur chaque enfant, mais les enfants avec le même `Order` paramètre sont organisés dans l’ordre où ils apparaissent dans le `Children` collection.

### <a name="the-basis-property"></a>La propriété de base

Le [ `Basis` ](xref:Xamarin.Forms.FlexLayout.BasisProperty) propriété pouvant être liée jointe indique la quantité d’espace qui est allouée à un enfant de le `FlexLayout` sur l’axe principal. La taille spécifiée par le `Basis` propriété est la taille de l’axe principal du parent `FlexLayout`. Par conséquent, `Basis` indique la largeur d’un enfant lorsque les enfants sont organisés dans des lignes ou la hauteur lorsque les enfants sont organisées en colonnes.

Le `Basis` propriété est de type [ `FlexBasis` ](xref:Xamarin.Forms.FlexBasis), une structure. La taille peut être spécifiée dans les deux unités indépendantes du périphérique ou sous forme de pourcentage de la taille de la `FlexLayout`. La valeur par défaut de la `Basis` propriété est la propriété statique `FlexBasis.Auto`, ce qui signifie que l’enfant demandée par la largeur ou hauteur est utilisée.

Dans le code, vous pouvez définir le `Basis` propriété pour un `Label` nommé `label` à 40 unités indépendantes du périphérique comme suit :

```csharp
FlexLayout.SetBasis(label, new FlexBasis(40, false));
```

Le deuxième argument à la `FlexBasis` constructeur se nomme `isRelative` et indique si la taille est relative (`true`) ou absolu (`false`). L’argument a la valeur par défaut `false`, de sorte que vous pouvez également utiliser le code suivant :

```csharp
FlexLayout.SetBasis(label, new FlexBasis(40));
```

Une conversion implicite de `float` à `FlexBasis` est définie, vous pouvez le simplifier encore plus loin :

```csharp
FlexLayout.SetBasis(label, 40);
```

Vous pouvez définir la taille de 25 % de la `FlexLayout` parent comme suit :

```csharp
FlexLayout.SetBasis(label, new FlexBasis(0.25f, true));
```

Cette valeur fractionnaire doit être dans la plage de 0 à 1.

Dans XAML, vous pouvez utiliser un nombre pour une taille en unités indépendantes du périphérique :

```xaml
<Label ... FlexLayout.Basis="40" ... />
```

Ou vous pouvez spécifier un pourcentage de la plage de 0 à 100 % :

```xaml
<Label ... FlexLayout.Basis="25%" ... />
```

Le **base expérimenter** page de la **[FlexLayoutDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** exemple vous permet de faire des essais avec le `Basis` propriété. La page affiche une colonne incluse dans un wrapper de cinq `Label` éléments avec des ALTERNANCES de couleurs d’arrière-plan et de premier plan. Deux `Slider` éléments vous permettent de spécifier `Basis` valeurs pour les deuxième et quatrième `Label`:

[![La base de l’expérience Page](flex-layout-images/BasisExperiment.png "la base de l’expérience de Page")](flex-layout-images/BasisExperiment-Large.png#lightbox)

La capture d’écran d’iOS à gauche montre les deux `Label` éléments en cours donnés hauteurs en unités indépendantes du périphérique. L’écran Android montre les hauteurs sont une fraction de la hauteur totale de l’occasion du `FlexLayout`. Si le `Basis` est défini à 100%, l’enfant est la hauteur `FlexLayout`du et est renvoyé à la colonne suivante et occupe toute la hauteur de cette colonne, comme le montre la capture d’écran UWP : Il semble que les cinq enfants soient disposés sur une ligne, mais ils sont en fait organisés en cinq colonnes.

### <a name="the-grow-property"></a>La propriété de la croissance

Le [ `Grow` ](xref:Xamarin.Forms.FlexLayout.GrowProperty) propriété pouvant être liée jointe est de type `int`. La valeur par défaut est 0, et la valeur doit être supérieure ou égale à 0.

Le `Grow` propriété joue un rôle lorsque le `Wrap` propriété est définie sur `NoWrap` et la ligne des enfants a une largeur totale inférieure à la largeur de la `FlexLayout`, ou la colonne d’enfants a une hauteur plus courte que la `FlexLayout`. Le `Grow` propriété indique comment répartir l’espace restes parmi les enfants.

Dans le **expérience croître** page cinq `Label` éléments de couleurs alternées sont organisés dans une colonne et deux `Slider` éléments vous permettent d’ajuster la `Grow` propriété du deuxième et quatrième `Label`. La capture d’écran d’iOS à l’extrême gauche montre la valeur par défaut `Grow` propriétés 0 :

[![La Page de l’expérience à croître](flex-layout-images/GrowExperiment.png "la Page de l’expérience agrandissement")](flex-layout-images/GrowExperiment-Large.png#lightbox)

Si tout un enfant reçoit un nombre positif `Grow` valeur, alors que les enfants occupe tout l’espace restant, comme le montre la capture d’écran Android. Cet espace peut également être alloué entre deux ou plusieurs enfants. Dans la capture d’écran UWP, le `Grow` propriété du deuxième `Label` a la valeur 0,5, tandis que le `Grow` propriété du quatrième `Label` est 1,5, ce qui donne la quatrième `Label` trois fois plus de l’espace restant comme deuxième `Label`.

Comment la vue enfant utilise cet espace varie selon le type particulier d’enfant. Pour un `Label`, le texte peut être positionné dans l’espace total de la `Label` en utilisant les propriétés `HorizontalTextAlignment` et `VerticalTextAlignment`.

<a name="shrink" />

### <a name="the-shrink-property"></a>La propriété de réduction

Le [ `Shrink` ](xref:Xamarin.Forms.FlexLayout.ShrinkProperty) propriété pouvant être liée jointe est de type `int`. La valeur par défaut est 1, et la valeur doit être supérieure ou égale à 0.

Le `Shrink` propriété joue un rôle lorsque le `Wrap` propriété est définie sur `NoWrap` et la largeur d’agrégation d’une ligne des enfants est supérieure à la largeur de la `FlexLayout`, ou la hauteur d’agrégation d’une colonne unique d’enfants est supérieure à la hauteur de la `FlexLayout`. Normalement le `FlexLayout` affiche ces enfants en limitant les membres de leur taille. Le `Shrink` propriété peut indiquer les enfants sont prioritaires dans s’afficher à leur taille complète.

Le **expérience réduire** page crée une `FlexLayout` avec une seule ligne de cinq `Label` enfants qui nécessitent davantage d’espace que la `FlexLayout` la largeur. La capture d’écran d’iOS à gauche montre tous les `Label` éléments avec les valeurs par défaut de 1 :

[![La réduction expérimenter Page](flex-layout-images/ShrinkExperiment.png "la réduction expérimenter de Page")](flex-layout-images/ShrinkExperiment-Large.png#lightbox)

Dans la capture d’écran Android, le `Shrink` valeur pour le deuxième `Label` est définie sur 0 et que `Label` s’affiche dans sa pleine chasse. En outre, la quatrième `Label` reçoit un `Shrink` valeur supérieure à un, et il a réduit automatiquement. La capture d’écran UWP montre à la fois `Label` éléments étant données une `Shrink` la valeur 0 pour leur permettre d’être affichés dans leur taille complète, si cela est possible.

Vous pouvez définir à la fois le `Grow` et `Shrink` valeurs pour prendre en charge des situations où les tailles d’agrégation enfant peuvent parfois être inférieure ou parfois supérieure à la taille de la `FlexLayout`.

## <a name="css-styling-with-flexlayout"></a>Styles CSS avec FlexLayout

Vous pouvez utiliser la [styles CSS](~/xamarin-forms/user-interface/styles/css/index.md) fonctionnalité introduite avec 3.0 de Xamarin.Forms par `FlexLayout`. Le **CSS les éléments de catalogue** page de la **[FlexLayoutDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** exemple duplique la disposition de la **éléments de catalogue** page, mais avec une CSS feuille de style pour la plupart des styles :

[![Page des éléments de catalogue de la CSS](flex-layout-images/CssCatalogItems.png "Page des éléments de catalogue de la CSS")](flex-layout-images/CssCatalogItems-Large.png#lightbox)

La version d’origine **CatalogItemsPage.xaml** fichier possède cinq `Style` définitions dans son `Resources` section avec 15 `Setter` objets. Dans le **CssCatalogItemsPage.xaml** fichier, ce qui a été réduite à deux `Style` définitions avec seulement quatre `Setter` objets. Ces styles complément la feuille de style CSS pour les propriétés qui la fonctionnalité de style CSS de Xamarin.Forms actuellement ne prennent en charge :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:FlexLayoutDemos"
             x:Class="FlexLayoutDemos.CssCatalogItemsPage"
             Title="CSS Catalog Items">
    <ContentPage.Resources>
        <StyleSheet Source="CatalogItemsStyles.css" />

        <Style TargetType="Frame">
            <Setter Property="BorderColor" Value="Blue" />
            <Setter Property="CornerRadius" Value="15" />
        </Style>

        <Style TargetType="Button">
            <Setter Property="Text" Value="LEARN MORE" />
            <Setter Property="BorderRadius" Value="20" />
        </Style>
    </ContentPage.Resources>

    <ScrollView Orientation="Both">
        <FlexLayout>
            <Frame>
                <FlexLayout Direction="Column">
                    <Label Text="Seated Monkey" StyleClass="header" />
                    <Label Text="This monkey is laid back and relaxed, and likes to watch the world go by." />
                    <Label Text="  &#x2022; Doesn't make a lot of noise" />
                    <Label Text="  &#x2022; Often smiles mysteriously" />
                    <Label Text="  &#x2022; Sleeps sitting up" />
                    <Image Source="{local:ImageResource FlexLayoutDemos.Images.SeatedMonkey.jpg}" />
                    <Label StyleClass="empty" />
                    <Button />
                </FlexLayout>
            </Frame>

            <Frame>
                <FlexLayout Direction="Column">
                    <Label Text="Banana Monkey" StyleClass="header" />
                    <Label Text="Watch this monkey eat a giant banana." />
                    <Label Text="  &#x2022; More fun than a barrel of monkeys" />
                    <Label Text="  &#x2022; Banana not included" />
                    <Image Source="{local:ImageResource FlexLayoutDemos.Images.Banana.jpg}" />
                    <Label StyleClass="empty" />
                    <Button />
                </FlexLayout>
            </Frame>

            <Frame>
                <FlexLayout Direction="Column">
                    <Label Text="Face-Palm Monkey" StyleClass="header" />
                    <Label Text="This monkey reacts appropriately to ridiculous assertions and actions." />
                    <Label Text="  &#x2022; Cynical but not unfriendly" />
                    <Label Text="  &#x2022; Seven varieties of grimaces" />
                    <Label Text="  &#x2022; Doesn't laugh at your jokes" />
                    <Image Source="{local:ImageResource FlexLayoutDemos.Images.FacePalm.jpg}" />
                    <Label StyleClass="empty" />
                    <Button />
                </FlexLayout>
            </Frame>
        </FlexLayout>
    </ScrollView>
</ContentPage>
```

La feuille de style CSS est référencée dans la première ligne de la `Resources` section :

```xaml
<StyleSheet Source="CatalogItemsStyles.css" />
```

Notez également que les deux éléments dans chacun des trois éléments incluent `StyleClass` paramètres :

```xaml
<Label Text="Seated Monkey" StyleClass="header" />
···
<Label StyleClass="empty" />
```

Ils se rapportent aux sélecteurs dans le **CatalogItemsStyles.css** feuille de style :

```css
frame {
    width: 300;
    height: 480;
    background-color: lightyellow;
    margin: 10;
}

label {
    margin: 4 0;
}

label.header {
    margin: 8 0;
    font-size: large;
    color: blue;
}

label.empty {
    flex-grow: 1;
}

image {
    height: 180;
    order: -1;
    align-self: center;
}

button {
    font-size: large;
    color: white;
    background-color: green;
}
```

Plusieurs `FlexLayout` jointes propriétés pouvant être liées sont référencées ici. Dans le `label.empty` sélecteur, vous verrez la `flex-grow` attribut, qui définit le style vide `Label` pour fournir un espace vide ci-dessus le `Button`. Le `image` sélecteur contient un `order` attribut et un `align-self` attribut, qui correspondent aux `FlexLayout` attaché propriétés pouvant être liées.

Vous avez vu que vous pouvez définir des propriétés directement sur le `FlexLayout` et vous pouvez définir des propriétés pouvant être liées jointes sur les enfants d’un `FlexLayout`. Ou bien, vous pouvez définir ces propriétés indirectement à l’aide de styles de basée sur XAML traditionnels ou des styles CSS. Ce qui est important doit connaître et comprendre ces propriétés. Ces propriétés sont ce qui rend le `FlexLayout` réellement flexible.

## <a name="flexlayout-with-xamarinuniversity"></a>FlexLayout avec Xamarin.University

> [!VIDEO https://youtube.com/embed/Ng3sel_5D_0]

**Vidéo de disposition Xamarin. Forms 3,0 Flex**

## <a name="related-links"></a>Liens connexes

- [FlexLayoutDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)
