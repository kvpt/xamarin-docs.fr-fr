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
ms.openlocfilehash: 75249966c6506bc33ea06c7cfa9c398bd7eb8045
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029485"
---
# <a name="the-xamarinforms-flexlayout"></a>Xamarin. Forms FlexLayout

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)

_Utilisez FlexLayout pour empiler ou encapsuler une collection de vues enfants._

Xamarin. Forms [`FlexLayout`](xref:Xamarin.Forms.FlexLayout) est une nouveauté de la version 3,0 de Xamarin. Forms. Il est basé sur le [module de disposition de zone flexible](https://www.w3.org/TR/css-flexbox-1/)CSS, communément appelé _disposition flexible_ ou _zone flexible_, donc appelé, car il comprend de nombreuses options flexibles pour organiser les enfants dans la disposition.

`FlexLayout` est semblable au [`StackLayout`](~/xamarin-forms/user-interface/layouts/stack-layout.md) Xamarin. Forms dans la mesure où il peut réorganiser ses enfants horizontalement et verticalement dans une pile. Toutefois, le `FlexLayout` peut également encapsuler ses enfants s’il y a trop d’un grand nombre pour tenir sur une seule ligne ou colonne, et possède également de nombreuses options pour l’orientation, l’alignement et l’adaptation à différentes tailles d’écran.

`FlexLayout` dérive de [`Layout<View>`](xref:Xamarin.Forms.Layout`1) et hérite d’une propriété [`Children`](xref:Xamarin.Forms.Layout`1.Children) de type `IList<View>`.

`FlexLayout` définit six propriétés pouvant être liées public et cinq propriétés pouvant être liées qui affectent la taille, l’orientation et l’alignement de ses éléments enfants. (Si vous n’êtes pas familiarisé avec les propriétés pouvant être liées, consultez l’article **[propriétés jointes](~/xamarin-forms/xaml/attached-properties.md)** .) Ces propriétés sont décrites en détail dans les sections ci-dessous sur **[les propriétés pouvant être liées, en détail](#bindable-properties)** , ainsi que **[les propriétés pouvant être liées qui sont attachées en détail](#attached-properties)** . Toutefois, cet article commence par une section sur certains **[scénarios d’utilisation courants](#common-scenarios)** de `FlexLayout` qui décrivent un grand nombre de ces propriétés de manière plus informelle. Vers la fin de l’article, vous verrez comment combiner des `FlexLayout` avec des [feuilles de style CSS](~/xamarin-forms/user-interface/styles/css/index.md).

<a name="common-scenarios" />

## <a name="common-usage-scenarios"></a>Scénarios d’utilisation courants

L’exemple de programme **[FlexLayoutDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** contient plusieurs pages qui illustrent des utilisations courantes de `FlexLayout` et vous permettent d’expérimenter ses propriétés.

### <a name="using-flexlayout-for-a-simple-stack"></a>Utilisation de FlexLayout pour une pile simple

La page de **pile simple** montre comment `FlexLayout` pouvez substituer une `StackLayout` mais avec un balisage plus simple. Tout ce qui se trouve dans cet exemple est défini dans la page XAML. Le `FlexLayout` contient quatre enfants :

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

- La propriété [`Direction`](xref:Xamarin.Forms.FlexLayout.Direction) est définie sur une valeur de l’énumération [`FlexDirection`](xref:Xamarin.Forms.FlexDirection) . La valeur par défaut est `Row`, Si vous affectez à la propriété la valeur `Column`, les enfants du `FlexLayout` sont disposés dans une seule colonne d’éléments.

    Lorsque les éléments d’un `FlexLayout` sont disposés dans une colonne, le `FlexLayout` est dit d’avoir un _axe principal_ vertical et un _axe_horizontal.

- La propriété [`AlignItems`](xref:Xamarin.Forms.FlexLayout.AlignItems) est de type [`FlexAlignItems`](xref:Xamarin.Forms.FlexAlignItems) et spécifie la manière dont les éléments sont alignés sur l’axe croisé. L’option `Center` fait en sorte que chaque élément soit centré horizontalement.

    Si vous utilisiez un `StackLayout` plutôt qu’un `FlexLayout` pour cette tâche, vous devez centrer tous les éléments en affectant la propriété `HorizontalOptions` de chaque élément à `Center`. La propriété `HorizontalOptions` ne fonctionne pas pour les enfants d’un `FlexLayout`, mais la propriété `AlignItems` unique remplit le même objectif. Si nécessaire, vous pouvez utiliser la `AlignSelf` propriété pouvant être liée attachée pour remplacer la propriété `AlignItems` pour des éléments individuels :

    ```xaml
    <Label Text="FlexLayout in Action"
           FontSize="Large"
           FlexLayout.AlignSelf="Start" />
    ```

    Une fois cette modification apportée, la `Label` est positionnée sur le bord gauche du `FlexLayout` lorsque l’ordre de lecture est de gauche à droite.

- La propriété [`JustifyContent`](xref:Xamarin.Forms.FlexLayout.JustifyContent) est de type [`FlexJustify`](xref:Xamarin.Forms.FlexJustify)et spécifie la manière dont les éléments sont disposés sur l’axe principal. L’option `SpaceEvenly` alloue tout l’espace vertical restant de manière égale entre tous les éléments, et au-dessus du premier élément, et en dessous du dernier élément.

    Si vous utilisiez un `StackLayout`, vous devez affecter la propriété `VerticalOptions` de chaque élément à `CenterAndExpand` pour obtenir un effet similaire. Toutefois, l’option `CenterAndExpand` alloue deux fois plus d’espace entre chaque élément qu’avant le premier élément et après le dernier élément. Vous pouvez simuler l’option `CenterAndExpand` de `VerticalOptions` en affectant à la propriété `JustifyContent` de `FlexLayout` la valeur `SpaceAround`.

Ces propriétés de `FlexLayout` sont abordées plus en détail dans la section **[les propriétés pouvant être liées sont décrites en détail](#bindable-properties)** ci-dessous.

### <a name="using-flexlayout-for-wrapping-items"></a>Utilisation de FlexLayout pour l’encapsulage d’éléments

La page Retour à la **photo** de l’exemple **[FlexLayoutDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** montre comment `FlexLayout` peut encapsuler ses enfants sur des lignes ou des colonnes supplémentaires. Le fichier XAML instancie le `FlexLayout` et lui assigne deux propriétés :

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

La propriété `Direction` de cette `FlexLayout` n’est pas définie. par conséquent, elle a le paramètre par défaut `Row`, ce qui signifie que les enfants sont disposés en lignes et que l’axe principal est horizontal.

La propriété [`Wrap`](xref:Xamarin.Forms.FlexLayout.Wrap) est d’un type énumération [`FlexWrap`](xref:Xamarin.Forms.FlexWrap). Si le nombre d’éléments à insérer sur une ligne est trop important, alors ce paramètre de propriété entraîne l’encapsulation des éléments sur la ligne suivante.

Notez que le `FlexLayout` est un enfant d’un `ScrollView`. Si le nombre de lignes à ajuster sur la page est trop important, la `ScrollView` a une propriété de `Orientation` par défaut de `Vertical` et autorise le défilement vertical.

La propriété `JustifyContent` alloue de l’espace restant sur l’axe principal (axe horizontal) afin que chaque élément soit entouré d’une quantité d’espace vide.

Le fichier code-behind accède à une collection d’exemples de photos et les ajoute à la collection `Children` des `FlexLayout` :

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

La page de **disposition Saint Grail** de l’exemple **[FlexLayoutDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** illustre une implémentation simple de cette disposition à l’aide d’une `FlexLayout` imbriquée dans une autre. Étant donné que cette page est conçue pour un téléphone en mode portrait, les zones situées à gauche et à droite de la zone de contenu sont uniquement de 50 pixels de largeur :

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

La première `FlexLayout` dans le fichier XAML a un axe vertical vertical et contient trois enfants organisés dans une colonne. Il s’agit de l’en-tête, du corps de la page et du pied de page. L' `FlexLayout` imbriqué a un axe principal horizontal avec trois enfants organisés en une ligne.

Trois propriétés pouvant être liées sont démontrées dans ce programme :

- La propriété pouvant être liée `Order` est définie sur le premier `BoxView`. Cette propriété est un entier avec une valeur par défaut de 0. Vous pouvez utiliser cette propriété pour modifier l’ordre de la disposition. En général, les développeurs préfèrent que le contenu de la page apparaisse dans le balisage avant les éléments de navigation et à côté des éléments. Si vous affectez à la propriété `Order` du premier `BoxView` une valeur inférieure à celle des autres frères, celle-ci apparaît en tant que premier élément de la ligne. De même, vous pouvez vous assurer qu’un élément apparaît en dernier en affectant à la propriété `Order` une valeur supérieure à ses frères.

- La propriété pouvant être liée `Basis` est définie sur les deux éléments `BoxView` pour leur attribuer une largeur de 50 pixels. Cette propriété est de type `FlexBasis`, une structure qui définit une propriété statique de type `FlexBasis` nommée `Auto`, qui est la valeur par défaut. Vous pouvez utiliser `Basis` pour spécifier une taille en pixels ou un pourcentage qui indique l’espace occupé par l’élément sur l’axe principal. Elle est appelée _base_ , car elle spécifie une taille d’élément qui est la base de toute disposition suivante.

- La propriété `Grow` est définie sur le `Layout` imbriqué et sur le `Label` enfant représentant le contenu. Cette propriété est de type `float` et a une valeur par défaut de 0. Lorsqu’elle est définie sur une valeur positive, tout l’espace restant le long de l’axe principal est alloué à cet élément et aux frères avec des valeurs positives de `Grow`. L’espace est alloué proportionnellement aux valeurs, à l’instar de la spécification en étoile dans un `Grid`.

    La première `Grow` propriété jointe est définie sur le `FlexLayout` imbriqué, ce qui indique que cette `FlexLayout` doit occuper tout l’espace vertical inutilisé dans le `FlexLayout` externe. La deuxième `Grow` propriété jointe est définie sur le `Label` représentant le contenu, ce qui indique que ce contenu doit occuper tout l’espace horizontal inutilisé dans le `FlexLayout` interne.

    Il existe également une propriété pouvant être liée `Shrink` jointe, que vous pouvez utiliser lorsque la taille des enfants dépasse la taille du `FlexLayout` mais que l’habillage n’est pas souhaité.

### <a name="catalog-items-with-flexlayout"></a>Éléments de catalogue avec FlexLayout

La page **éléments du catalogue** de l’exemple **[FlexLayoutDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** est similaire à l' [exemple 1 de la section 1,1 de la spécification de la zone de disposition CSS Flex](https://www.w3.org//TR/css-flexbox-1/#overview) , sauf qu’elle affiche une série d’images avec défilement horizontal et une description de trois singes. :

[![Page éléments du catalogue](flex-layout-images/CatalogItems.png "Page éléments du catalogue")](flex-layout-images/CatalogItems-Large.png#lightbox)

Chacun des trois singes est un `FlexLayout` contenu dans une `Frame` qui reçoit une hauteur et une largeur explicites, et qui est également un enfant d’un `FlexLayout` plus grand. Dans ce fichier XAML, la plupart des propriétés des enfants `FlexLayout` sont spécifiées dans les styles, sauf un style implicite :

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

Le style implicite de l' `Image` comprend les paramètres de deux propriétés pouvant être liées pouvant être liées de `Flexlayout` :

```xaml
<Style TargetType="Image">
    <Setter Property="FlexLayout.Order" Value="-1" />
    <Setter Property="FlexLayout.AlignSelf" Value="Center" />
</Style>
```

Le paramètre `Order` de &ndash;1 provoque l’affichage de l’élément `Image` en premier dans chacune des vues de `FlexLayout` imbriquées, indépendamment de sa position dans la collection Children. La propriété `AlignSelf` de `Center` amène le `Image` à être centré dans le `FlexLayout`. Cela remplace le paramètre de la propriété `AlignItems`, qui a une valeur par défaut de `Stretch`, ce qui signifie que les enfants `Label` et `Button` sont étirés à la largeur totale du `FlexLayout`.

Dans chacune des trois vues de `FlexLayout`, un `Label` vide précède le `Button`, mais il a un paramètre de `Grow` de 1. Cela signifie que tous les espaces verticaux supplémentaires sont alloués à ce `Label` vide, ce qui entraîne l’envoi des `Button` en bas.

<a name="bindable-properties" />

## <a name="the-bindable-properties-in-detail"></a>Propriétés pouvant être liées en détail

Maintenant que vous avez vu certaines applications courantes de `FlexLayout`, les propriétés de `FlexLayout` peuvent être explorées plus en détail.
`FlexLayout` définit six propriétés pouvant être liées que vous définissez sur le `FlexLayout` lui-même, en code ou en XAML, pour contrôler l’orientation et l’alignement. (L’une de ces propriétés, [`Position`](xref:Xamarin.Forms.FlexLayout.Position), n’est pas abordée dans cet article.)

Vous pouvez expérimenter les cinq autres propriétés pouvant être liées à l’aide de la page **expérience** de l’exemple **[FlexLayoutDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** . Cette page vous permet d’ajouter ou de supprimer des enfants d’un `FlexLayout` et de définir des combinaisons de cinq propriétés pouvant être liées. Tous les enfants de la `FlexLayout` sont `Label` des vues de différentes couleurs et tailles, avec la propriété `Text` définie sur un nombre correspondant à sa position dans la collection `Children`.

Au démarrage du programme, cinq `Picker` vues affichent les valeurs par défaut de ces cinq propriétés de `FlexLayout`. La `FlexLayout` vers le bas de l’écran contient trois enfants :

[![Page expérience : par défaut](flex-layout-images/ExperimentDefault.png "Page expérience-par défaut")](flex-layout-images/ExperimentDefault-Large.png#lightbox)

Chaque `Label` affiche un arrière-plan gris qui indique l’espace alloué à ce `Label` dans le `FlexLayout`. L’arrière-plan du `FlexLayout` lui-même est le bleu d’Alice. Elle occupe la totalité de la partie inférieure de la page, à l’exception d’une petite marge à gauche et à droite.

<a name="direction" />

### <a name="the-direction-property"></a>La propriété direction

La propriété [`Direction`](xref:Xamarin.Forms.FlexLayout.Direction) est de type [`FlexDirection`](xref:Xamarin.Forms.FlexDirection), une énumération avec quatre membres :

- `Column`
- `ColumnReverse` (ou « retour à la colonne » en XAML)
- `Row`, la valeur par défaut
- `RowReverse` (ou « retour à la ligne » en XAML)

En XAML, vous pouvez spécifier la valeur de cette propriété en utilisant les noms des membres de l’énumération en minuscules, en majuscules ou en minuscules, ou vous pouvez utiliser deux chaînes supplémentaires indiquées entre parenthèses qui sont identiques aux indicateurs CSS. (Les chaînes « Column-inversé » et « Row-inversé » sont définies dans la classe [`FlexDirectionTypeConverter`](xref:Xamarin.Forms.FlexDirectionTypeConverter) utilisée par l’analyseur XAML.)

Voici la page de l' **expérience** affichée (de gauche à droite), la direction de l' `Row`, la direction du `Column` et la direction de la `ColumnReverse` :

[![Page de l’expérience : direction](flex-layout-images/ExperimentDirection.png "Direction de la page de l’expérience")](flex-layout-images/ExperimentDirection-Large.png#lightbox)

Notez que pour les options `Reverse`, les éléments démarrent à droite ou en bas.

<a name="wrap" />

### <a name="the-wrap-property"></a>La propriété Wrap

La propriété [`Wrap`](xref:Xamarin.Forms.FlexLayout.Wrap) est de type [`FlexWrap`](xref:Xamarin.Forms.FlexWrap), une énumération avec trois membres :

- `NoWrap`, la valeur par défaut
- `Wrap`
- `Reverse` (ou « Wrap-inversé » en XAML)

De gauche à droite, ces écrans affichent les options `NoWrap`, `Wrap` et `Reverse` pour 12 enfants :

[![Page de l’expérience : retour à la ligne](flex-layout-images/ExperimentWrap.png "Page d’expérience-retour à la ligne")](flex-layout-images/ExperimentWrap-Large.png#lightbox)

Lorsque la propriété `Wrap` est définie sur `NoWrap` et que l’axe principal est limité (comme dans ce programme), et que l’axe principal n’est pas assez grand ou plus grand pour correspondre à tous les enfants, le `FlexLayout` tente de réduire les éléments , comme le montre la capture d’écran iOS. Vous pouvez contrôler la réduction des éléments à l’aide de la [`Shrink`](#shrink) propriété pouvant être liée.

<a name="justify-content" />

### <a name="the-justifycontent-property"></a>Propriété JustifyContent

La propriété [`JustifyContent`](xref:Xamarin.Forms.FlexLayout.JustifyContent) est de type [`FlexJustify`](xref:Xamarin.Forms.FlexJustify), une énumération avec six membres :

- `Start` (ou « Flex-Start » en XAML), la valeur par défaut
- `Center`
- `End` (ou « Flex-end » en XAML)
- `SpaceBetween` (ou « espace-entre » en XAML)
- `SpaceAround` (ou « espace » en XAML)
- `SpaceEvenly`

Cette propriété spécifie la manière dont les éléments sont espacés sur l’axe principal, qui est l’axe horizontal dans cet exemple :

[![Page de l’expérience : justifier le contenu](flex-layout-images/ExperimentJustifyContent.png "Page de l’expérience-justifier le contenu")](flex-layout-images/ExperimentJustifyContent-Large.png#lightbox)

Dans les trois captures d’écran, la propriété `Wrap` est définie sur `Wrap`. La `Start` par défaut est indiquée dans la capture d’écran Android précédente. La capture d’écran iOS présente l’option `Center` : tous les éléments sont déplacés vers le centre. Les trois autres options qui commencent par le mot `Space` allouez l’espace supplémentaire non occupé par les éléments. `SpaceBetween` alloue l’espace de manière égale entre les éléments ;  `SpaceAround` place un espace égal autour de chaque élément, tandis que `SpaceEvenly` place un espace égal entre chaque élément, et avant le premier élément et après le dernier élément de la ligne.

<a name="align-items" />

### <a name="the-alignitems-property"></a>Propriété AlignItems

La propriété [`AlignItems`](xref:Xamarin.Forms.FlexLayout.AlignItems) est de type [`FlexAlignItems`](xref:Xamarin.Forms.FlexAlignItems), une énumération avec quatre membres :

- `Stretch`, la valeur par défaut
- `Center`
- `Start` (ou « Flex-Start » en XAML)
- `End` (ou « Flex-end » en XAML)

Il s’agit de l’une des deux propriétés (l’autre [`AlignContent`](#align-content)) qui indique comment les enfants sont alignés sur l’axe croisé. Dans chaque ligne, les enfants sont étirés (comme indiqué dans la capture d’écran précédente) ou alignés sur le début, le centre ou la fin de chaque élément, comme indiqué dans les trois captures d’écran suivantes :

[![Page de l’expérience : aligner les éléments](flex-layout-images/ExperimentAlignItems.png "Page expérience-aligner les éléments")](flex-layout-images/ExperimentAlignItems-Large.png#lightbox)

Dans la capture d’écran iOS, les sommets de tous les enfants sont alignés. Dans les captures d’écran Android, les éléments sont centrés verticalement en fonction de l’enfant le plus grand. Dans la capture d’écran UWP, les bas de tous les éléments sont alignés.

Pour tout élément individuel, le paramètre `AlignItems` peut être substitué avec la propriété pouvant être liée [`AlignSelf`](#align-self) jointe.

<a name="align-content" />

### <a name="the-aligncontent-property"></a>Propriété AlignContent

La propriété [`AlignContent`](xref:Xamarin.Forms.FlexLayout.AlignContent) est de type [`FlexAlignContent`](xref:Xamarin.Forms.FlexAlignContent), une énumération avec sept membres :

- `Stretch`, la valeur par défaut
- `Center`
- `Start` (ou « Flex-Start » en XAML)
- `End` (ou « Flex-end » en XAML)
- `SpaceBetween` (ou « espace-entre » en XAML)
- `SpaceAround` (ou « espace » en XAML)
- `SpaceEvenly`

Comme `AlignItems`, la propriété `AlignContent` aligne également les enfants sur l’axe croisé, mais affecte les lignes ou les colonnes entières :

[![Page de l’expérience : aligner le contenu](flex-layout-images/ExperimentAlignContent.png "Page expérience-aligner le contenu")](flex-layout-images/ExperimentAlignContent-Large.png#lightbox)

Dans la capture d’écran iOS, les deux lignes se trouvent en haut. dans la capture d’écran Android, elles se trouvent au centre. et dans la capture d’écran UWP, elles se trouvent en bas. Les lignes peuvent également être espacées de différentes façons :

[![Page de l’expérience : aligner le contenu 2](flex-layout-images/ExperimentAlignContent2.png "Page expérience-aligner le contenu 2")](flex-layout-images/ExperimentAlignContent2-Large.png#lightbox)

La `AlignContent` n’a aucun effet lorsqu’il n’existe qu’une seule ligne ou colonne.

<a name="attached-properties" />

## <a name="the-attached-bindable-properties-in-detail"></a>Les propriétés pouvant être liées en pièce jointe en détail

`FlexLayout` définit cinq propriétés pouvant être liées en pièce jointe. Ces propriétés sont définies sur les enfants de la `FlexLayout` et se rapportent uniquement à cet enfant particulier.

<a name="align-self" />

### <a name="the-alignself-property"></a>Propriété AlignSelf

La propriété pouvant être liée [`AlignSelf`](xref:Xamarin.Forms.FlexLayout.AlignSelfProperty) est de type [`FlexAlignSelf`](xref:Xamarin.Forms.FlexAlignContent), une énumération avec cinq membres :

- `Auto`, la valeur par défaut
- `Stretch`
- `Center`
- `Start` (ou « Flex-Start » en XAML)
- `End` (ou « Flex-end » en XAML)

Pour tout enfant individuel de l' `FlexLayout`, ce paramètre de propriété remplace la propriété [`AlignItems`](#align-items) définie sur le `FlexLayout` lui-même. La valeur par défaut de `Auto` signifie utiliser le paramètre `AlignItems`.

Pour un élément `Label` nommé `label` (ou exemple), vous pouvez définir la propriété `AlignSelf` dans le code comme suit :

```csharp
FlexLayout.SetAlignSelf(label, FlexAlignSelf.Center);
```

Notez qu’il n’existe aucune référence au parent `FlexLayout` du `Label`. En XAML, vous définissez la propriété de la façon suivante :

```xaml
<Label ... FlexLayout.AlignSelf="Center" ... />
```

### <a name="the-order-property"></a>La propriété Order

La propriété [`Order`](xref:Xamarin.Forms.FlexLayout.OrderProperty) est de type `int`. La valeur par défaut est 0.

La propriété `Order` vous permet de modifier l’ordre de réorganisation des enfants des `FlexLayout`. En règle générale, les enfants d’un `FlexLayout` sont organisés dans l’ordre dans lequel ils apparaissent dans la collection `Children`. Vous pouvez remplacer cet ordre en affectant à la `Order` propriété pouvant être liée attachée une valeur entière différente de zéro sur un ou plusieurs enfants. Le `FlexLayout` réorganise ensuite ses enfants en fonction du paramètre de la propriété `Order` sur chaque enfant, mais les enfants ayant le même paramètre de `Order` sont disposés dans l’ordre dans lequel ils apparaissent dans la collection de `Children`.

### <a name="the-basis-property"></a>Propriété de base

La propriété pouvant être liée par [`Basis`](xref:Xamarin.Forms.FlexLayout.BasisProperty) indique la quantité d’espace allouée à un enfant du `FlexLayout` sur l’axe principal. La taille spécifiée par la propriété `Basis` correspond à la taille le long de l’axe principal du `FlexLayout` parent. Par conséquent, `Basis` indique la largeur d’un enfant lorsque les enfants sont disposés en lignes, ou la hauteur lorsque les enfants sont disposés en colonnes.

La propriété `Basis` est de type [`FlexBasis`](xref:Xamarin.Forms.FlexBasis), une structure. La taille peut être spécifiée dans des unités indépendantes du périphérique ou sous la forme d’un pourcentage de la taille de la `FlexLayout`. La valeur par défaut de la propriété `Basis` est la propriété statique `FlexBasis.Auto`, ce qui signifie que la largeur ou la hauteur demandée de l’enfant est utilisée.

Dans le code, vous pouvez définir la propriété `Basis` pour une `Label` nommée `label` sur 40 unités indépendantes du périphérique, comme suit :

```csharp
FlexLayout.SetBasis(label, new FlexBasis(40, false));
```

Le deuxième argument du constructeur `FlexBasis` est nommé `isRelative` et indique si la taille est relative (`true`) ou absolue (`false`). L’argument a une valeur par défaut de `false`, donc vous pouvez également utiliser le code suivant :

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

La page **expérience de base** de l’exemple **[FlexLayoutDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** vous permet d’expérimenter la propriété `Basis`. La page affiche une colonne encapsulée de cinq éléments de `Label` avec des couleurs de premier plan et d’arrière-plan alternées. Deux éléments `Slider` vous permettent de spécifier `Basis` valeurs pour les deuxième et quatrième `Label` :

[![Page expérience de base](flex-layout-images/BasisExperiment.png "Page expérience de base")](flex-layout-images/BasisExperiment-Large.png#lightbox)

La capture d’écran iOS sur la gauche montre les deux éléments `Label` en fonction des unités indépendantes des appareils. L’écran Android affiche des hauteurs qui représentent une fraction de la hauteur totale du `FlexLayout`. Si la `Basis` est définie à 100%, l’enfant est la hauteur de la `FlexLayout`et est renvoyé à la colonne suivante et occupe toute la hauteur de cette colonne, comme le montre la capture d’écran UWP : il apparaît comme si les cinq enfants étaient disposés sur une ligne. , mais ils sont en fait organisés en cinq colonnes.

### <a name="the-grow-property"></a>La propriété Grow

La propriété pouvant être liée [`Grow`](xref:Xamarin.Forms.FlexLayout.GrowProperty) est de type `int`. La valeur par défaut est 0 et la valeur doit être supérieure ou égale à 0.

La propriété `Grow` joue un rôle lorsque la propriété `Wrap` a la valeur `NoWrap` et que la largeur totale de la ligne des enfants est inférieure à la largeur du `FlexLayout`, ou que la colonne des enfants a une hauteur plus petite que la `FlexLayout`. La propriété `Grow` indique comment répartir l’espace restant entre les enfants.

Dans la page **agrandir l’expérience** , cinq `Label` éléments de couleurs alternées sont disposés dans une colonne et deux éléments `Slider` vous permettent d’ajuster la propriété `Grow` des deuxième et quatrième `Label`. La capture d’écran iOS située à l’extrême gauche montre les propriétés de `Grow` par défaut de 0 :

[![Page de l’expérience de développement](flex-layout-images/GrowExperiment.png "Page de l’expérience de développement")](flex-layout-images/GrowExperiment-Large.png#lightbox)

Si une valeur `Grow` positive est affectée à un enfant, celui-ci occupe tout l’espace restant, comme le montre la capture d’écran Android. Cet espace peut également être alloué entre deux enfants ou plus. Dans la capture d’écran UWP, la propriété `Grow` du deuxième `Label` a la valeur 0,5, tandis que la propriété `Grow` de la quatrième `Label` est 1,5, ce qui donne au quatrième `Label` trois fois plus d’espace que le deuxième `Label`.

La façon dont la vue enfant utilise cet espace dépend du type particulier de l’enfant. Pour un `Label`, le texte peut être positionné dans l’espace total du `Label` à l’aide des propriétés `HorizontalTextAlignment` et `VerticalTextAlignment`.

<a name="shrink" />

### <a name="the-shrink-property"></a>La propriété Shrink

La propriété pouvant être liée [`Shrink`](xref:Xamarin.Forms.FlexLayout.ShrinkProperty) est de type `int`. La valeur par défaut est 1, et la valeur doit être supérieure ou égale à 0.

La propriété `Shrink` joue un rôle lorsque la propriété `Wrap` a la valeur `NoWrap` et que la largeur globale d’une ligne d’enfants est supérieure à la largeur du `FlexLayout`, ou la hauteur d’agrégat d’une colonne d’enfants est supérieure à la hauteur de `FlexLayout`. Normalement, le `FlexLayout` affichera ces enfants en restreignant leurs tailles. La propriété `Shrink` peut indiquer les enfants dont la priorité est affichée en fonction de leur taille complète.

La page de l' **expérience de réduction** crée une `FlexLayout` avec une seule ligne de cinq `Label` enfants qui requièrent plus d’espace que la largeur de la `FlexLayout`. La capture d’écran iOS située à gauche montre tous les éléments de `Label` dont la valeur par défaut est 1 :

[![Page de l’expérience de réduction](flex-layout-images/ShrinkExperiment.png "Page de l’expérience de réduction")](flex-layout-images/ShrinkExperiment-Large.png#lightbox)

Dans la capture d’écran Android, la valeur `Shrink` du deuxième `Label` est définie sur 0, et cette `Label` est affichée en pleine largeur. En outre, la quatrième `Label` reçoit une valeur `Shrink` supérieure à 1 et a été réduite. La capture d’écran UWP indique à la fois `Label` éléments reçoivent une valeur `Shrink` égale à 0 pour leur permettre d’être affichés dans leur taille maximale, si cela est possible.

Vous pouvez définir les valeurs `Grow` et `Shrink` pour tenir compte des situations où la taille des enfants agrégés peut parfois être inférieure ou parfois supérieure à celle de la `FlexLayout`.

## <a name="css-styling-with-flexlayout"></a>Styles CSS avec FlexLayout

Vous pouvez utiliser la fonctionnalité de [style CSS](~/xamarin-forms/user-interface/styles/css/index.md) introduite avec Xamarin. forms 3,0 en relation avec `FlexLayout`. La page **éléments du catalogue CSS** de l’exemple **[FlexLayoutDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** duplique la disposition de la page **éléments du catalogue** , mais avec une feuille de style CSS pour la plupart des styles :

[![Page éléments du catalogue CSS](flex-layout-images/CssCatalogItems.png "Page éléments du catalogue CSS")](flex-layout-images/CssCatalogItems-Large.png#lightbox)

Le fichier **CatalogItemsPage. Xaml** d’origine a cinq définitions de `Style` dans sa section `Resources` avec 15 objets `Setter`. Dans le fichier **CssCatalogItemsPage. Xaml** , qui a été réduit à deux `Style` définitions avec seulement quatre objets `Setter`. Ces styles complètent la feuille de style CSS pour les propriétés que la fonctionnalité de style CSS Xamarin. Forms ne prend actuellement pas en charge :

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

La feuille de style CSS est référencée sur la première ligne de la section `Resources` :

```xaml
<StyleSheet Source="CatalogItemsStyles.css" />
```

Notez également que deux éléments dans chacun des trois éléments incluent des paramètres de `StyleClass` :

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

Plusieurs `FlexLayout` des propriétés pouvant être liées sont référencées ici. Dans le sélecteur de `label.empty`, vous verrez l’attribut `flex-grow`, qui style une `Label` vide pour fournir un espace vide au-dessus de la `Button`. Le sélecteur de `image` contient un attribut `order` et un attribut `align-self`, qui correspondent tous les deux à `FlexLayout` propriétés pouvant être liées attachées.

Vous avez vu que vous pouvez définir des propriétés directement sur la `FlexLayout` et vous pouvez définir des propriétés pouvant être liées attachées sur les enfants d’un `FlexLayout`. Ou vous pouvez définir ces propriétés indirectement à l’aide de styles XAML traditionnels ou de styles CSS. Il est important de savoir et de comprendre ces propriétés. Ces propriétés permettent aux `FlexLayout` véritablement flexibles.

## <a name="flexlayout-with-xamarinuniversity"></a>FlexLayout avec Xamarin. University

> [!VIDEO https://youtube.com/embed/Ng3sel_5D_0]

**Vidéo de disposition Xamarin. Forms 3,0 Flex**

## <a name="related-links"></a>Liens connexes

- [FlexLayoutDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)
