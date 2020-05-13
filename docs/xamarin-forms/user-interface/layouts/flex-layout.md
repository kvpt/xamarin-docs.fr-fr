---
title: Xamarin. Forms FlexLayout
description: Utilisez FlexLayout pour empiler ou encapsuler une collection de vues enfants.
ms.prod: xamarin
ms.assetid: 6A91EA70-268C-462C-AAAF-F8DA011403F8
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 05/07/2018
ms.openlocfilehash: 507f78bf887d8d11e93a5a6a1f7d074c55e69360
ms.sourcegitcommit: 83cf2a4d99546751c6394510a463a2b2a8bf75b8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83149968"
---
# <a name="the-xamarinforms-flexlayout"></a>Xamarin. Forms FlexLayout

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)

_Utilisez FlexLayout pour empiler ou encapsuler une collection de vues enfants._

Xamarin. Forms [`FlexLayout`](xref:Xamarin.Forms.FlexLayout) est une nouveauté de la version 3,0 de Xamarin. Forms. Il est basé sur le [module de disposition de zone flexible](https://www.w3.org/TR/css-flexbox-1/)CSS, communément appelé _disposition flexible_ ou _zone flexible_, donc appelé, car il comprend de nombreuses options flexibles pour organiser les enfants dans la disposition.

`FlexLayout`est semblable à Xamarin. Forms [`StackLayout`](~/xamarin-forms/user-interface/layouts/stacklayout.md) dans la mesure où il peut réorganiser ses enfants horizontalement et verticalement dans une pile. Toutefois, le `FlexLayout` peut également encapsuler ses enfants s’il y a trop d’un grand nombre pour tenir dans une seule ligne ou colonne, et possède également de nombreuses options pour l’orientation, l’alignement et l’adaptation à différentes tailles d’écran.

`FlexLayout`dérive de [`Layout<View>`](xref:Xamarin.Forms.Layout`1) et hérite d’une [`Children`](xref:Xamarin.Forms.Layout`1.Children) propriété de type `IList<View>` .

`FlexLayout`définit six propriétés pouvant être liées publiques et cinq propriétés pouvant être liées qui affectent la taille, l’orientation et l’alignement de ses éléments enfants. (Si vous n’êtes pas familiarisé avec les propriétés pouvant être liées, consultez l’article **[propriétés jointes](~/xamarin-forms/xaml/attached-properties.md)**.) Ces propriétés sont décrites en détail dans les sections ci-dessous sur **[les propriétés pouvant être liées, en détail](#bindable-properties)** , ainsi que **[les propriétés pouvant être liées qui sont attachées en détail](#attached-properties)**. Toutefois, cet article commence par une section sur certains **[scénarios d’utilisation courants](#common-scenarios)** de `FlexLayout` qui décrivent un grand nombre de ces propriétés de manière plus informelle. Vers la fin de l’article, vous verrez comment combiner `FlexLayout` avec les [feuilles de style CSS](~/xamarin-forms/user-interface/styles/css/index.md).

<a name="common-scenarios" />

## <a name="common-usage-scenarios"></a>Scénarios d’utilisation courants

L’exemple de programme **[FlexLayoutDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** contient plusieurs pages qui illustrent des utilisations courantes de `FlexLayout` et vous permettent d’expérimenter ses propriétés.

### <a name="using-flexlayout-for-a-simple-stack"></a>Utilisation de FlexLayout pour une pile simple

La page de **pile simple** montre comment `FlexLayout` peut se substituer à un `StackLayout` mais avec un balisage plus simple. Tout ce qui se trouve dans cet exemple est défini dans la page XAML. `FlexLayout`Contient quatre enfants :

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

Voici la page s’exécutant sur iOS, Android et la plateforme Windows universelle :

[![Page de la pile simple](flex-layout-images/SimpleStack.png "Page de la pile simple")](flex-layout-images/SimpleStack-Large.png#lightbox)

Trois propriétés de `FlexLayout` sont affichées dans le fichier **SimpleStackPage. Xaml** :

- La [`Direction`](xref:Xamarin.Forms.FlexLayout.Direction) propriété est définie sur une valeur de l' [`FlexDirection`](xref:Xamarin.Forms.FlexDirection) énumération. Par défaut, il s’agit de `Row`. Si vous affectez la valeur à la propriété `Column` , les enfants de l’objet `FlexLayout` doivent être disposés dans une seule colonne d’éléments.

    Lorsque les éléments d’un `FlexLayout` sont disposés dans une colonne, `FlexLayout` est dit qu’il a un _axe_ vertical vertical et un _axe_horizontal.

- La [`AlignItems`](xref:Xamarin.Forms.FlexLayout.AlignItems) propriété est de type [`FlexAlignItems`](xref:Xamarin.Forms.FlexAlignItems) et spécifie la manière dont les éléments sont alignés sur l’axe croisé. L' `Center` option fait en sorte que chaque élément soit centré horizontalement.

    Si vous utilisiez un `StackLayout` plutôt qu’un `FlexLayout` pour cette tâche, vous devez centrer tous les éléments en affectant la `HorizontalOptions` propriété de chaque élément à `Center` . La `HorizontalOptions` propriété ne fonctionne pas pour les enfants d’un `FlexLayout` , mais la `AlignItems` propriété unique remplit le même objectif. Si nécessaire, vous pouvez utiliser la `AlignSelf` propriété attachée pouvant être attachée pour remplacer la `AlignItems` propriété pour des éléments individuels :

    ```xaml
    <Label Text="FlexLayout in Action"
           FontSize="Large"
           FlexLayout.AlignSelf="Start" />
    ```

    Avec cette modification, celle `Label` -ci est positionnée sur le bord gauche du `FlexLayout` lorsque l’ordre de lecture est de gauche à droite.

- La [`JustifyContent`](xref:Xamarin.Forms.FlexLayout.JustifyContent) propriété est de type [`FlexJustify`](xref:Xamarin.Forms.FlexJustify) et spécifie la manière dont les éléments sont disposés sur l’axe principal. L' `SpaceEvenly` option alloue tout l’espace vertical restant de manière égale entre tous les éléments, et au-dessus du premier élément, et en dessous du dernier élément.

    Si vous utilisiez un `StackLayout` , vous devez assigner la `VerticalOptions` propriété de chaque élément à `CenterAndExpand` pour obtenir un effet similaire. Toutefois `CenterAndExpand` , l’option allouerait deux fois plus d’espace entre chaque élément qu’avant le premier élément et après le dernier élément. Vous pouvez imiter l' `CenterAndExpand` option de `VerticalOptions` en affectant `JustifyContent` à la propriété de la valeur `FlexLayout` `SpaceAround` .

Ces `FlexLayout` propriétés sont décrites plus en détail dans la section des **[propriétés pouvant être liées en détail](#bindable-properties)** ci-dessous.

### <a name="using-flexlayout-for-wrapping-items"></a>Utilisation de FlexLayout pour l’encapsulage d’éléments

La page Retour à la **photo** de l’exemple **[FlexLayoutDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** montre comment `FlexLayout` peut encapsuler ses enfants sur des lignes ou des colonnes supplémentaires. Le fichier XAML instancie `FlexLayout` et assigne deux propriétés de celui-ci :

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

La `Direction` propriété de ce `FlexLayout` n’est pas définie. par conséquent, elle a le paramètre par défaut `Row` , ce qui signifie que les enfants sont disposés en lignes et que l’axe principal est horizontal.

La [`Wrap`](xref:Xamarin.Forms.FlexLayout.Wrap) propriété est d’un type énumération [`FlexWrap`](xref:Xamarin.Forms.FlexWrap) . Si le nombre d’éléments à insérer sur une ligne est trop important, alors ce paramètre de propriété entraîne l’encapsulation des éléments sur la ligne suivante.

Notez que `FlexLayout` est un enfant d’un `ScrollView` . Si le nombre de lignes à ajuster sur la page est trop important, la `ScrollView` propriété a une valeur par défaut `Orientation` `Vertical` et autorise le défilement vertical.

La `JustifyContent` propriété alloue de l’espace restant sur l’axe principal (axe horizontal) afin que chaque élément soit entouré d’une quantité d’espace vide.

Le fichier code-behind accède à une collection d’exemples de photos et les ajoute à la `Children` collection des `FlexLayout` éléments suivants :

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

Voici le programme en cours d’exécution, défilant progressivement de haut en bas :

[![Page de retour à la photo](flex-layout-images/PhotoWrapping.png "Page de retour à la photo")](flex-layout-images/PhotoWrapping-Large.png#lightbox)

### <a name="page-layout-with-flexlayout"></a>Mise en page avec FlexLayout

Il existe une disposition standard dans la conception Web appelée le [_Saint Grail_](https://en.wikipedia.org/wiki/Holy_grail_(web_design)) , car il s’agit d’un format de disposition qui est très souhaitable, mais souvent difficile à réaliser avec la perfection. La disposition se compose d’un en-tête en haut de la page et d’un pied de page en bas, qui s’étend à la largeur totale de la page. L’occupant du centre de la page est le contenu principal, mais souvent avec un menu en colonnes à gauche du contenu et des informations supplémentaires (parfois appelées « zone de _réserve_ ») à droite. [La section 5.4.1 de la spécification de disposition de zone flexible CSS](https://www.w3.org/TR/css-flexbox-1/#order-accessibility) décrit comment la disposition Saint Grail peut être obtenue avec une zone flexible.

La page de **disposition Saint Grail** de l’exemple **[FlexLayoutDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** illustre une implémentation simple de cette disposition en utilisant une `FlexLayout` imbriquée dans une autre. Étant donné que cette page est conçue pour un téléphone en mode portrait, les zones situées à gauche et à droite de la zone de contenu sont uniquement de 50 pixels de largeur :

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

[![Page de disposition Saint Grail](flex-layout-images/HolyGrailLayout.png "Page de disposition Saint Grail")](flex-layout-images/HolyGrailLayout-Large.png#lightbox)

Les zones de navigation et de côté sont rendues avec un `BoxView` à gauche et à droite.

Le premier `FlexLayout` dans le fichier XAML a un axe vertical vertical et contient trois enfants organisés dans une colonne. Il s’agit de l’en-tête, du corps de la page et du pied de page. Le imbriqué `FlexLayout` a un axe principal horizontal avec trois enfants organisés dans une ligne.

Trois propriétés pouvant être liées sont démontrées dans ce programme :

- La `Order` propriété pouvant être liée est définie sur la première `BoxView` . Cette propriété est un entier avec une valeur par défaut de 0. Vous pouvez utiliser cette propriété pour modifier l’ordre de la disposition. En général, les développeurs préfèrent que le contenu de la page apparaisse dans le balisage avant les éléments de navigation et à côté des éléments. La définition de la `Order` propriété de la première sur `BoxView` une valeur inférieure à celle des autres frères fait apparaître comme premier élément de la ligne. De même, vous pouvez vous assurer qu’un élément apparaît en dernier en affectant `Order` à la propriété une valeur supérieure à ses frères.

- La `Basis` propriété pouvant être liée est définie sur les deux `BoxView` éléments pour lui attribuer une largeur de 50 pixels. Cette propriété est de type `FlexBasis` , une structure qui définit une propriété statique de type `FlexBasis` nommée `Auto` , qui est la valeur par défaut. Vous pouvez utiliser `Basis` pour spécifier une taille en pixels ou un pourcentage qui indique l’espace occupé par l’élément sur l’axe principal. Elle est appelée _base_ , car elle spécifie une taille d’élément qui est la base de toute disposition suivante.

- La `Grow` propriété est définie sur le imbriqué `Layout` et sur l' `Label` enfant représentant le contenu. Cette propriété est de type `float` et a une valeur par défaut de 0. Lorsqu’elle est définie sur une valeur positive, tout l’espace restant le long de l’axe principal est alloué à cet élément et aux frères avec des valeurs positives de `Grow` . L’espace est alloué proportionnellement aux valeurs, à l’instar de la spécification en étoile dans un `Grid` .

    La première `Grow` propriété jointe est définie sur le imbriqué `FlexLayout` , ce qui indique qu’il `FlexLayout` doit occuper tout l’espace vertical inutilisé dans le externe `FlexLayout` . La deuxième `Grow` propriété jointe est définie sur le `Label` qui représente le contenu, ce qui indique que ce contenu doit occuper tout l’espace horizontal inutilisé dans le interne `FlexLayout` .

    Il existe également une propriété pouvant être liée semblable `Shrink` , que vous pouvez utiliser lorsque la taille des enfants est supérieure à la taille du, mais le renvoi à la `FlexLayout` valeur n’est pas souhaité.

### <a name="catalog-items-with-flexlayout"></a>Éléments de catalogue avec FlexLayout

La page **éléments du catalogue** de l’exemple **[FlexLayoutDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** est similaire à l' [exemple 1 de la section 1,1 de la spécification de la zone de disposition CSS Flex](https://www.w3.org//TR/css-flexbox-1/#overview) , à ceci près qu’elle affiche une série d’images avec défilement horizontal et des descriptions de trois singes :

[![Page éléments du catalogue](flex-layout-images/CatalogItems.png "Page éléments du catalogue")](flex-layout-images/CatalogItems-Large.png#lightbox)

Chacun des trois singes est un `FlexLayout` contenu dans un `Frame` qui est donné à une hauteur et une largeur explicites, et qui est également un enfant d’un plus grand `FlexLayout` . Dans ce fichier XAML, la plupart des propriétés des `FlexLayout` enfants sont spécifiées dans les styles, sauf un style implicite :

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

Le style implicite pour le `Image` comprend les paramètres de deux propriétés pouvant être liées qui sont attachées `Flexlayout` :

```xaml
<Style TargetType="Image">
    <Setter Property="FlexLayout.Order" Value="-1" />
    <Setter Property="FlexLayout.AlignSelf" Value="Center" />
</Style>
```

La `Order` valeur &ndash; 1 provoque l’affichage de l' `Image` élément en premier dans chacune des vues imbriquées `FlexLayout` , indépendamment de sa position dans la collection d’enfants. La `AlignSelf` propriété de `Center` entraîne le `Image` centrage du à l’intérieur de `FlexLayout` . Cela remplace le paramètre de la `AlignItems` propriété, dont la valeur par défaut est, ce `Stretch` qui signifie que les `Label` `Button` enfants et sont étirés à la largeur totale du `FlexLayout` .

Dans chacune des trois `FlexLayout` vues, un vide `Label` précède `Button` , mais le paramètre a la `Grow` valeur 1. Cela signifie que tous les espaces verticaux supplémentaires sont alloués à ce vide `Label` , ce qui `Button` permet d’effectuer un push du en bas.

<a name="bindable-properties" />

## <a name="the-bindable-properties-in-detail"></a>Propriétés pouvant être liées en détail

Maintenant que vous avez vu certaines applications courantes de `FlexLayout` , les propriétés de `FlexLayout` peuvent être explorées plus en détail.
`FlexLayout`définit six propriétés pouvant être liées que vous définissez sur `FlexLayout` lui-même, en code ou en XAML, pour contrôler l’orientation et l’alignement. (L’une de ces propriétés, [`Position`](xref:Xamarin.Forms.FlexLayout.Position) , n’est pas abordée dans cet article.)

Vous pouvez expérimenter les cinq autres propriétés pouvant être liées à l’aide de la page **expérience** de l’exemple **[FlexLayoutDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** . Cette page vous permet d’ajouter ou de supprimer des enfants d’un `FlexLayout` et de définir des combinaisons de cinq propriétés pouvant être liées. Tous les enfants de `FlexLayout` sont `Label` des vues de différentes couleurs et tailles, la propriété ayant la `Text` valeur un nombre correspondant à sa position dans la `Children` collection.

Au démarrage du programme, cinq `Picker` vues affichent les valeurs par défaut de ces cinq `FlexLayout` Propriétés. Le `FlexLayout` vers le bas de l’écran contient trois enfants :

[![Page expérience : par défaut](flex-layout-images/ExperimentDefault.png "Page expérience-par défaut")](flex-layout-images/ExperimentDefault-Large.png#lightbox)

Chacun des `Label` affichages présente un arrière-plan gris qui indique l’espace alloué à celui-ci `Label` dans le `FlexLayout` . L’arrière-plan du `FlexLayout` lui-même est le bleu d’Alice. Elle occupe la totalité de la partie inférieure de la page, à l’exception d’une petite marge à gauche et à droite.

<a name="direction" />

### <a name="the-direction-property"></a>La propriété direction

La [`Direction`](xref:Xamarin.Forms.FlexLayout.Direction) propriété est de type [`FlexDirection`](xref:Xamarin.Forms.FlexDirection) , une énumération avec quatre membres :

- `Column`
- `ColumnReverse`(ou « colonne-inversion » en XAML)
- `Row`, la valeur par défaut
- `RowReverse`(ou « Row-inversé » en XAML)

En XAML, vous pouvez spécifier la valeur de cette propriété en utilisant les noms des membres de l’énumération en minuscules, en majuscules ou en minuscules, ou vous pouvez utiliser deux chaînes supplémentaires indiquées entre parenthèses qui sont identiques aux indicateurs CSS. (Les chaînes « Column-inversé » et « Row-inversé » sont définies dans la [`FlexDirectionTypeConverter`](xref:Xamarin.Forms.FlexDirectionTypeConverter) classe utilisée par l’analyseur XAML.)

Voici la page d' **expérimentation** affichée (de gauche à droite), la `Row` direction, la `Column` direction et la `ColumnReverse` direction :

[![Page de l’expérience : direction](flex-layout-images/ExperimentDirection.png "Direction de la page de l’expérience")](flex-layout-images/ExperimentDirection-Large.png#lightbox)

Notez que pour les `Reverse` options, les éléments commencent à droite ou en bas.

<a name="wrap" />

### <a name="the-wrap-property"></a>La propriété Wrap

La [`Wrap`](xref:Xamarin.Forms.FlexLayout.Wrap) propriété est de type [`FlexWrap`](xref:Xamarin.Forms.FlexWrap) , une énumération avec trois membres :

- `NoWrap`, la valeur par défaut
- `Wrap`
- `Reverse`(ou « Wrap-inversé » en XAML)

De gauche à droite, ces écrans affichent `NoWrap` les `Wrap` options, et `Reverse` pour 12 enfants :

[![Page de l’expérience : retour à la ligne](flex-layout-images/ExperimentWrap.png "Page d’expérience-retour à la ligne")](flex-layout-images/ExperimentWrap-Large.png#lightbox)

Lorsque la `Wrap` propriété a la valeur `NoWrap` et que l’axe principal est limité (comme dans ce programme), et que l’axe principal n’est pas assez grand ou grand pour contenir tous les enfants, le `FlexLayout` tente de réduire les éléments, comme le montre la capture d’écran iOS. Vous pouvez contrôler la réduction des éléments avec la [`Shrink`](#shrink) propriété pouvant être liée attachée.

<a name="justify-content" />

### <a name="the-justifycontent-property"></a>Propriété JustifyContent

La [`JustifyContent`](xref:Xamarin.Forms.FlexLayout.JustifyContent) propriété est de type [`FlexJustify`](xref:Xamarin.Forms.FlexJustify) , une énumération avec six membres :

- `Start`(ou « Flex-Start » en XAML), la valeur par défaut
- `Center`
- `End`(ou « Flex-end » en XAML)
- `SpaceBetween`(ou « espace-entre » en XAML)
- `SpaceAround`(ou « espace-autour » en XAML)
- `SpaceEvenly`

Cette propriété spécifie la manière dont les éléments sont espacés sur l’axe principal, qui est l’axe horizontal dans cet exemple :

[![Page de l’expérience : justifier le contenu](flex-layout-images/ExperimentJustifyContent.png "Page de l’expérience-justifier le contenu")](flex-layout-images/ExperimentJustifyContent-Large.png#lightbox)

Dans les trois captures d’écran, la `Wrap` propriété a la valeur `Wrap` . La `Start` valeur par défaut est indiquée dans la capture d’écran Android précédente. La capture d’écran iOS présente l' `Center` option : tous les éléments sont déplacés vers le centre. Les trois autres options qui commencent par le mot `Space` allouez l’espace supplémentaire non occupé par les éléments. `SpaceBetween`alloue l’espace de manière égale entre les éléments ; `SpaceAround`place un espace égal autour de chaque élément, tout en `SpaceEvenly` plaçant un espace égal entre chaque élément, et avant le premier élément et après le dernier élément de la ligne.

<a name="align-items" />

### <a name="the-alignitems-property"></a>Propriété AlignItems

La [`AlignItems`](xref:Xamarin.Forms.FlexLayout.AlignItems) propriété est de type [`FlexAlignItems`](xref:Xamarin.Forms.FlexAlignItems) , une énumération avec quatre membres :

- `Stretch`, la valeur par défaut
- `Center`
- `Start`(ou « Flex-Start » en XAML)
- `End`(ou « Flex-end » en XAML)

Il s’agit de l’une des deux propriétés (l’autre [`AlignContent`](#align-content) ) qui indique comment les enfants sont alignés sur l’axe croisé. Dans chaque ligne, les enfants sont étirés (comme indiqué dans la capture d’écran précédente) ou alignés sur le début, le centre ou la fin de chaque élément, comme indiqué dans les trois captures d’écran suivantes :

[![Page de l’expérience : aligner les éléments](flex-layout-images/ExperimentAlignItems.png "Page expérience-aligner les éléments")](flex-layout-images/ExperimentAlignItems-Large.png#lightbox)

Dans la capture d’écran iOS, les sommets de tous les enfants sont alignés. Dans les captures d’écran Android, les éléments sont centrés verticalement en fonction de l’enfant le plus grand. Dans la capture d’écran UWP, les bas de tous les éléments sont alignés.

Pour tout élément individuel, le `AlignItems` paramètre peut être remplacé par la [`AlignSelf`](#align-self) propriété pouvant être liée attachée.

<a name="align-content" />

### <a name="the-aligncontent-property"></a>Propriété AlignContent

La [`AlignContent`](xref:Xamarin.Forms.FlexLayout.AlignContent) propriété est de type [`FlexAlignContent`](xref:Xamarin.Forms.FlexAlignContent) , une énumération avec sept membres :

- `Stretch`, la valeur par défaut
- `Center`
- `Start`(ou « Flex-Start » en XAML)
- `End`(ou « Flex-end » en XAML)
- `SpaceBetween`(ou « espace-entre » en XAML)
- `SpaceAround`(ou « espace-autour » en XAML)
- `SpaceEvenly`

Comme `AlignItems` , la `AlignContent` propriété aligne également les enfants sur l’axe transversal, mais affecte les lignes ou les colonnes entières :

[![Page de l’expérience : aligner le contenu](flex-layout-images/ExperimentAlignContent.png "Page expérience-aligner le contenu")](flex-layout-images/ExperimentAlignContent-Large.png#lightbox)

Dans la capture d’écran iOS, les deux lignes se trouvent en haut. dans la capture d’écran Android, elles se trouvent au centre. et dans la capture d’écran UWP, elles se trouvent en bas. Les lignes peuvent également être espacées de différentes façons :

[![Page de l’expérience : aligner le contenu 2](flex-layout-images/ExperimentAlignContent2.png "Page expérience-aligner le contenu 2")](flex-layout-images/ExperimentAlignContent2-Large.png#lightbox)

Le n' `AlignContent` a aucun effet lorsqu’il n’y a qu’une seule ligne ou colonne.

<a name="attached-properties" />

## <a name="the-attached-bindable-properties-in-detail"></a>Les propriétés pouvant être liées en pièce jointe en détail

`FlexLayout`définit cinq propriétés pouvant être liées en pièce jointe. Ces propriétés sont définies sur les enfants de `FlexLayout` et se rapportent uniquement à cet enfant particulier.

<a name="align-self" />

### <a name="the-alignself-property"></a>Propriété AlignSelf

La [`AlignSelf`](xref:Xamarin.Forms.FlexLayout.AlignSelfProperty) propriété pouvant être liée attachée est de type [`FlexAlignSelf`](xref:Xamarin.Forms.FlexAlignContent) , une énumération avec cinq membres :

- `Auto`, la valeur par défaut
- `Stretch`
- `Center`
- `Start`(ou « Flex-Start » en XAML)
- `End`(ou « Flex-end » en XAML)

Pour tout enfant individuel de `FlexLayout` , ce paramètre de propriété remplace la [`AlignItems`](#align-items) propriété définie sur le `FlexLayout` lui-même. La valeur par défaut de `Auto` signifie que utilise le `AlignItems` paramètre.

Pour un `Label` élément nommé `label` (ou exemple), vous pouvez définir la `AlignSelf` propriété dans le code comme suit :

```csharp
FlexLayout.SetAlignSelf(label, FlexAlignSelf.Center);
```

Notez qu’il n’existe aucune référence au `FlexLayout` parent du `Label` . En XAML, vous définissez la propriété de la façon suivante :

```xaml
<Label ... FlexLayout.AlignSelf="Center" ... />
```

### <a name="the-order-property"></a>La propriété Order

La [`Order`](xref:Xamarin.Forms.FlexLayout.OrderProperty) propriété est de type `int` . La valeur par défaut est 0.

La `Order` propriété vous permet de modifier l’ordre dans lequel les enfants du `FlexLayout` sont disposés. En règle générale, les enfants d’un `FlexLayout` sont disposés dans l’ordre dans lequel ils apparaissent dans la `Children` collection. Vous pouvez remplacer cet ordre en affectant `Order` à la propriété pouvant être liée attachée une valeur entière différente de zéro sur un ou plusieurs enfants. `FlexLayout`Réorganise ensuite ses enfants en fonction du paramètre de la `Order` propriété sur chaque enfant, mais les enfants avec le même `Order` paramètre sont disposés dans l’ordre dans lequel ils apparaissent dans la `Children` collection.

### <a name="the-basis-property"></a>Propriété de base

La [`Basis`](xref:Xamarin.Forms.FlexLayout.BasisProperty) propriété pouvant être liée liée indique la quantité d’espace allouée à un enfant du `FlexLayout` sur l’axe principal. La taille spécifiée par la `Basis` propriété correspond à la taille le long de l’axe principal du parent `FlexLayout` . Par conséquent, `Basis` indique la largeur d’un enfant lorsque les enfants sont disposés en lignes, ou la hauteur lorsque les enfants sont disposés en colonnes.

La `Basis` propriété est de type [`FlexBasis`](xref:Xamarin.Forms.FlexBasis) , une structure. La taille peut être spécifiée dans des unités indépendantes du périphérique ou sous la forme d’un pourcentage de la taille de `FlexLayout` . La valeur par défaut de la `Basis` propriété est la propriété statique, ce qui `FlexBasis.Auto` signifie que la largeur ou la hauteur demandée de l’enfant est utilisée.

Dans le code, vous pouvez définir la `Basis` propriété d' `Label` une `label` unité nommée sur 40 unités indépendantes du périphérique, comme suit :

```csharp
FlexLayout.SetBasis(label, new FlexBasis(40, false));
```

Le deuxième argument du `FlexBasis` constructeur est nommé `isRelative` et indique si la taille est relative ( `true` ) ou absolue ( `false` ). L’argument a la valeur par défaut `false` , ce qui vous permet également d’utiliser le code suivant :

```csharp
FlexLayout.SetBasis(label, new FlexBasis(40));
```

Une conversion implicite de `float` en `FlexBasis` est définie, ce qui vous permet de la simplifier encore plus loin :

```csharp
FlexLayout.SetBasis(label, 40);
```

Vous pouvez définir la taille sur 25% du `FlexLayout` parent comme suit :

```csharp
FlexLayout.SetBasis(label, new FlexBasis(0.25f, true));
```

Cette valeur fractionnaire doit être comprise entre 0 et 1.

En XAML, vous pouvez utiliser un nombre pour une taille en unités indépendantes du périphérique :

```xaml
<Label ... FlexLayout.Basis="40" ... />
```

Ou vous pouvez spécifier un pourcentage dans la plage comprise entre 0% et 100% :

```xaml
<Label ... FlexLayout.Basis="25%" ... />
```

La page **expérience de base** de l’exemple **[FlexLayoutDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** vous permet d’expérimenter la `Basis` propriété. La page affiche une colonne encapsulée de cinq `Label` éléments avec des couleurs de premier plan et d’arrière-plan alternées. Deux `Slider` éléments vous permettent `Basis` de spécifier des valeurs pour les deuxième et quatrième `Label` :

[![Page expérience de base](flex-layout-images/BasisExperiment.png "Page expérience de base")](flex-layout-images/BasisExperiment-Large.png#lightbox)

La capture d’écran iOS située à gauche montre les deux `Label` éléments qui ont des hauteurs en unités indépendantes des appareils. L’écran Android affiche des hauteurs qui représentent une fraction de la hauteur totale du `FlexLayout` . Si le `Basis` est défini à 100%, l’enfant est la hauteur du et est `FlexLayout` renvoyé à la colonne suivante et occupe toute la hauteur de cette colonne, comme le montre la capture d’écran UWP : il apparaît comme si les cinq enfants étaient disposés dans une ligne, mais ils sont en fait organisés en cinq colonnes.

### <a name="the-grow-property"></a>La propriété Grow

La [`Grow`](xref:Xamarin.Forms.FlexLayout.GrowProperty) propriété pouvant être liée attachée est de type `int` . La valeur par défaut est 0 et la valeur doit être supérieure ou égale à 0.

La `Grow` propriété joue un rôle lorsque la `Wrap` propriété a la valeur `NoWrap` et que la ligne des enfants a une largeur totale inférieure à la largeur du `FlexLayout` ou que la colonne des enfants a une hauteur plus petite que le `FlexLayout` . La `Grow` propriété indique comment répartir l’espace restant entre les enfants.

Dans la page **agrandir l’expérience** , cinq `Label` éléments de couleurs alternées sont disposés dans une colonne et deux `Slider` éléments vous permettent d’ajuster la `Grow` propriété des deuxième et quatrième éléments `Label` . La capture d’écran iOS située à l’extrême gauche montre les propriétés par défaut `Grow` 0 :

[![Page de l’expérience de développement](flex-layout-images/GrowExperiment.png "Page de l’expérience de développement")](flex-layout-images/GrowExperiment-Large.png#lightbox)

Si une valeur positive est affectée à un enfant `Grow` , celui-ci occupe tout l’espace restant, comme le montre la capture d’écran Android. Cet espace peut également être alloué entre deux enfants ou plus. Dans la capture d’écran UWP, la `Grow` propriété du second `Label` est définie sur 0,5, tandis que la `Grow` propriété du quatrième est égale à `Label` 1,5, ce qui donne au quatrième `Label` trois fois plus d’espace que la seconde `Label` .

La façon dont la vue enfant utilise cet espace dépend du type particulier de l’enfant. Pour un `Label` , le texte peut être positionné dans l’espace total du `Label` à l’aide des propriétés `HorizontalTextAlignment` et `VerticalTextAlignment` .

<a name="shrink" />

### <a name="the-shrink-property"></a>La propriété Shrink

La [`Shrink`](xref:Xamarin.Forms.FlexLayout.ShrinkProperty) propriété pouvant être liée attachée est de type `int` . La valeur par défaut est 1, et la valeur doit être supérieure ou égale à 0.

La `Shrink` propriété joue un rôle lorsque la `Wrap` propriété a la valeur `NoWrap` et que la largeur d’agrégat d’une ligne d’enfants est supérieure à la largeur du `FlexLayout` , ou la hauteur d’agrégat d’une colonne d’enfants est supérieure à la hauteur de `FlexLayout` . Normalement, le `FlexLayout` affiche ces enfants en limitant leurs tailles. La `Shrink` propriété peut indiquer les enfants dont la priorité est affichée en fonction de leur taille complète.

La page de l' **expérience de réduction** crée un `FlexLayout` avec une seule ligne de cinq `Label` enfants qui requièrent plus d’espace que la `FlexLayout` largeur. La capture d’écran iOS située à gauche montre tous les `Label` éléments dont la valeur par défaut est 1 :

[![Page de l’expérience de réduction](flex-layout-images/ShrinkExperiment.png "Page de l’expérience de réduction")](flex-layout-images/ShrinkExperiment-Large.png#lightbox)

Dans la capture d’écran Android, la `Shrink` valeur de la seconde `Label` est définie sur 0, et s' `Label` affiche en pleine largeur. En outre, la quatrième `Label` valeur reçoit une `Shrink` valeur supérieure à 1 et elle a été réduite. La capture d’écran UWP indique que les deux `Label` éléments reçoivent la `Shrink` valeur 0 pour leur permettre d’être affichés dans leur taille maximale, si cela est possible.

Vous pouvez définir les `Grow` valeurs et `Shrink` pour tenir compte des situations où les tailles des enfants d’agrégats peuvent parfois être inférieures ou parfois supérieures à la taille de `FlexLayout` .

## <a name="css-styling-with-flexlayout"></a>Styles CSS avec FlexLayout

Vous pouvez utiliser la fonctionnalité de [style CSS](~/xamarin-forms/user-interface/styles/css/index.md) introduite avec Xamarin. forms 3,0 dans le cadre de la connexion à `FlexLayout` . La page **éléments du catalogue CSS** de l’exemple **[FlexLayoutDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** duplique la disposition de la page **éléments du catalogue** , mais avec une feuille de style CSS pour la plupart des styles :

[![Page éléments du catalogue CSS](flex-layout-images/CssCatalogItems.png "Page éléments du catalogue CSS")](flex-layout-images/CssCatalogItems-Large.png#lightbox)

Le fichier **CatalogItemsPage. Xaml** d’origine a cinq `Style` définitions dans sa `Resources` section avec 15 `Setter` objets. Dans le fichier **CssCatalogItemsPage. Xaml** , qui a été réduit à deux `Style` définitions avec seulement quatre `Setter` objets. Ces styles complètent la feuille de style CSS pour les propriétés que la fonctionnalité de style CSS Xamarin. Forms ne prend actuellement pas en charge :

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

La feuille de style CSS est référencée sur la première ligne de la `Resources` section :

```xaml
<StyleSheet Source="CatalogItemsStyles.css" />
```

Notez également que deux éléments dans chacun des trois éléments incluent des `StyleClass` Paramètres :

```xaml
<Label Text="Seated Monkey" StyleClass="header" />
···
<Label StyleClass="empty" />
```

Ceux-ci font référence aux sélecteurs de la feuille de style **CatalogItemsStyles. CSS** :

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

Plusieurs `FlexLayout` propriétés pouvant être liées sont référencées ici. Dans le `label.empty` Sélecteur, vous voyez l' `flex-grow` attribut, qui style un vide `Label` pour fournir un espace vide au-dessus de `Button` . Le `image` Sélecteur contient un `order` attribut et un `align-self` attribut, qui correspondent tous les deux à des `FlexLayout` propriétés pouvant être liées attachées.

Vous avez vu que vous pouvez définir des propriétés directement sur le `FlexLayout` et vous pouvez définir des propriétés pouvant être liées attachées sur les enfants d’un `FlexLayout` . Ou vous pouvez définir ces propriétés indirectement à l’aide de styles XAML traditionnels ou de styles CSS. Il est important de savoir et de comprendre ces propriétés. Ces propriétés sont les plus `FlexLayout` flexibles.

## <a name="flexlayout-with-xamarinuniversity"></a>FlexLayout avec Xamarin. University

> [!VIDEO https://youtube.com/embed/Ng3sel_5D_0]

**Vidéo de disposition Xamarin. Forms 3,0 Flex**

## <a name="related-links"></a>Liens connexes

- [FlexLayoutDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)
