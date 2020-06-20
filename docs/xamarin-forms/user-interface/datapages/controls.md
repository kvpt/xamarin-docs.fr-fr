---
title: Référence des contrôles DataPages
description: Cet article présente les contrôles qui sont disponibles dans le Xamarin.Forms package NuGet de DataPages.
ms.prod: xamarin
ms.assetid: 891615D0-E8BD-4ACC-A7F0-4C3725FBCC31
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 86b526fff305b195221aca3fb6a86ad0823cb145
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84569398"
---
# <a name="datapages-controls-reference"></a>Référence des contrôles DataPages

![](~/media/shared/preview.png "This API is currently in preview")

> [!IMPORTANT]
> DataPages requiert une Xamarin.Forms référence de thème à restituer. Cela implique l’installation de [ Xamarin.Forms . Package. base](https://www.nuget.org/packages/Xamarin.Forms.Theme.Base/) NuGet dans votre projet, suivi de l’un ou l’autre [ Xamarin.Forms . Theme. Light](https://www.nuget.org/packages/Xamarin.Forms.Theme.Light/) ou [ Xamarin.Forms . Packages NuGet Theme. Dark](https://www.nuget.org/packages/Xamarin.Forms.Theme.Dark/) .

Le Xamarin.Forms NuGet de DataPages comprend un certain nombre de contrôles qui peuvent tirer parti de la liaison de la source de données.

Pour utiliser ces contrôles en XAML, assurez-vous que l’espace de noms a été inclus, par exemple, dans la `xmlns:pages` déclaration ci-dessous :

```xaml
<ContentPage
    xmlns="http://xamarin.com/schemas/2014/forms"
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
    xmlns:pages="clr-namespace:Xamarin.Forms.Pages;assembly=Xamarin.Forms.Pages"
    x:Class="DataPagesDemo.Detail">
```

Les exemples ci-dessous incluent des `DynamicResource` références qui doivent exister dans le dictionnaire de ressources du projet pour fonctionner. Il existe également un exemple de création d’un [contrôle personnalisé](#custom-control-example).

## <a name="built-in-controls"></a>Contrôles intégrés

* [HeroImage](#heroimage)
* [ListItem](#listitem)

### <a name="heroimage"></a>HeroImage

Le `HeroImage` contrôle a quatre propriétés :

* Texte
* Détail
* ImageSource
* Aspect

```xaml
<pages:HeroImage
    ImageSource="{ DynamicResource HeroImageImage }"
    Text="Keith Ballinger"
    Detail="Xamarin"
/>
```

**Android**

![](controls-images/heroimage-light-android.png "Contrôle HeroImage sur Android") ![](controls-images/heroimage-dark-android.png "Contrôle HeroImage sur Android")

**iOS**

![](controls-images/heroimage-light-ios.png "Contrôle HeroImage sur iOS") ![](controls-images/heroimage-dark-ios.png "Contrôle HeroImage sur iOS")

### <a name="listitem"></a>ListItem

La `ListItem` disposition du contrôle est similaire aux lignes de liste ou de table iOS et Android natives. Toutefois, il peut également être utilisé comme affichage normal. Dans l’exemple de code ci-dessous, il est affiché dans un `StackLayout` , mais il peut également être utilisé dans les contrôles de liste scolling liés aux données.

Il existe cinq propriétés :

* Titre
* Détail
* ImageSource
* PlaceholdImageSource
* Aspect

```xaml
<StackLayout Spacing="0">
    <pages:ListItemControl
        Detail="Xamarin"
        ImageSource="{ DynamicResource UserImage }"
        Title="Miguel de Icaza"
        PlaceholdImageSource="{ DynamicResource IconImage }"
    />
```

Ces captures d’écran montrent les `ListItem` plateformes sur iOS et Android à l’aide des thèmes clairs et foncés :

**Android**

![](controls-images/listitem-light-android.png "Contrôle ListItem sur Android") ![](controls-images/listitem-dark-android.png "Contrôle ListItem sur Android")

**iOS**

![](controls-images/listitem-light-ios.png "Contrôle ListItem sur iOS") ![](controls-images/listitem-dark-ios.png "Contrôle ListItem sur iOS")

## <a name="custom-control-example"></a>Exemple de contrôle personnalisé

L’objectif de ce `CardView` contrôle personnalisé est de ressembler à un CardView Android natif.

Elle contient trois propriétés :

* Texte
* Détail
* ImageSource

L’objectif est un contrôle personnalisé qui ressemble au code ci-dessous (Notez qu’un personnalisé `xmlns:local` est requis qui référence l’assembly actuel) :

```xaml
<local:CardView
  ImageSource="{ DynamicResource CardViewImage }"
  Text="CardView Text"
  Detail="CardView Detail"
/>
```

Elle doit ressembler aux captures d’écran ci-dessous à l’aide de couleurs correspondant aux thèmes clairs et foncés intégrés :

**Android**

![](controls-images/cardview-light-android.png "Contrôle personnalisé CardView sur Android") ![](controls-images/cardview-dark-android.png "Contrôle personnalisé CardView sur Android")

**iOS**

![](controls-images/cardview-light-ios.png "Contrôle personnalisé CardView sur iOS") ![](controls-images/cardview-dark-ios.png "Contrôle personnalisé CardView sur iOS")

### <a name="building-the-custom-cardview"></a>Création du CardView personnalisé

1. [Sous-classe DataView](#1-dataview-subclass)
2. [Définir la police, la disposition et les marges](#2-define-font-layout-and-margins)
3. [Créer des styles pour les enfants du contrôle](#3-create-styles-for-the-controls-children)
4. [Créer le modèle de disposition de contrôle](#4-create-the-control-layout-template)
5. [Ajouter les ressources spécifiques au thème](#5-add-the-theme-specific-resources)
6. [Définir le ControlTemplate pour la classe CardView](#6-set-the-controltemplate-for-the-cardview-class)
7. [Ajouter le contrôle à une page](#7-add-the-control-to-a-page)

#### <a name="1-dataview-subclass"></a>1. sous-classe DataView

La sous-classe C# de `DataView` définit les propriétés pouvant être liées pour le contrôle.

```csharp
public class CardView : DataView
{
    public static readonly BindableProperty TextProperty =
        BindableProperty.Create ("Text", typeof (string), typeof (CardView), null, BindingMode.TwoWay);

    public string Text
    {
        get { return (string)GetValue (TextProperty); }
        set { SetValue (TextProperty, value); }
    }

    public static readonly BindableProperty DetailProperty =
        BindableProperty.Create ("Detail", typeof (string), typeof (CardView), null, BindingMode.TwoWay);

    public string Detail
    {
        get { return (string)GetValue (DetailProperty); }
        set { SetValue (DetailProperty, value); }
    }

    public static readonly BindableProperty ImageSourceProperty =
        BindableProperty.Create ("ImageSource", typeof (ImageSource), typeof (CardView), null, BindingMode.TwoWay);

    public ImageSource ImageSource
    {
        get { return (ImageSource)GetValue (ImageSourceProperty); }
        set { SetValue (ImageSourceProperty, value); }
    }

    public CardView()
    {
    }
}
```

#### <a name="2-define-font-layout-and-margins"></a>2. définir la police, la disposition et les marges

Le concepteur de contrôles va déterminer ces valeurs dans le cadre de la conception de l’interface utilisateur pour le contrôle personnalisé. Lorsque des spécifications spécifiques à la plateforme sont requises, l' `OnPlatform` élément est utilisé.

Notez que certaines valeurs font référence à `StaticResource` s. celles-ci seront définies à l' [étape 5](#5-add-the-theme-specific-resources).

```xml
<!-- CARDVIEW FONT SIZES -->
<OnPlatform x:TypeArguments="x:Double" x:Key="CardViewTextFontSize">
        <On Platform="iOS, Android" Value="15" />
</OnPlatform>

<OnPlatform x:TypeArguments="x:Double" x:Key="CardViewDetailFontSize">
        <On Platform="iOS, Android" Value="13" />
</OnPlatform>

<OnPlatform x:TypeArguments="Color"    x:Key="CardViewTextTextColor">
        <On Platform="iOS" Value="{StaticResource iOSCardViewTextTextColor}" />
        <On Platform="Android" Value="{StaticResource AndroidCardViewTextTextColor}" />
</OnPlatform>

<OnPlatform x:TypeArguments="Thickness"    x:Key="CardViewTextlMargin">
        <On Platform="iOS" Value="12,10,12,4" />
        <On Platform="Android" Value="20,0,20,5" />
</OnPlatform>

<OnPlatform x:TypeArguments="Color"    x:Key="CardViewDetailTextColor">
        <On Platform="iOS" Value="{StaticResource iOSCardViewDetailTextColor}" />
        <On Platform="Android" Value="{StaticResource AndroidCardViewDetailTextColor}" />
</OnPlatform>

<OnPlatform x:TypeArguments="Thickness"    x:Key="CardViewDetailMargin">
        <On Platform="iOS" Value="12,0,10,12" />
        <On Platform="Android" Value="20,0,20,20" />
</OnPlatform>

<OnPlatform x:TypeArguments="Color"    x:Key="CardViewBackgroundColor">
        <On Platform="iOS" Value="{StaticResource iOSCardViewBackgroundColor}" />
        <On Platform="Android" Value="{StaticResource AndroidCardViewBackgroundColor}" />
</OnPlatform>

<OnPlatform x:TypeArguments="x:Double" x:Key="CardViewShadowSize">
        <On Platform="iOS" Value="2" />
        <On Platform="Android" Value="5" />
</OnPlatform>

<OnPlatform x:TypeArguments="x:Double" x:Key="CardViewCornerRadius">
        <On Platform="iOS" Value="0" />
        <On Platform="Android" Value="4" />
</OnPlatform>

<OnPlatform x:TypeArguments="Color"    x:Key="CardViewShadowColor">
        <On Platform="iOS, Android" Value="#CDCDD1" />
</OnPlatform>
```

#### <a name="3-create-styles-for-the-controls-children"></a>3. créer des styles pour les enfants du contrôle

Référencez tous les éléments définis sur pour créer les enfants qui seront utilisés dans le contrôle personnalisé :

```xml
<!-- EXPLICIT STYLES (will be Classes) -->
<Style TargetType="Label" x:Key="CardViewTextStyle">
    <Setter Property="FontSize" Value="{ StaticResource CardViewTextFontSize }" />
    <Setter Property="TextColor" Value="{ StaticResource CardViewTextTextColor }" />
    <Setter Property="HorizontalOptions" Value="Start" />
    <Setter Property="Margin" Value="{ StaticResource CardViewTextlMargin }" />
    <Setter Property="HorizontalTextAlignment" Value="Start" />
</Style>

<Style TargetType="Label" x:Key="CardViewDetailStyle">
    <Setter Property="HorizontalTextAlignment" Value="Start" />
    <Setter Property="TextColor" Value="{ StaticResource CardViewDetailTextColor }" />
    <Setter Property="FontSize" Value="{ StaticResource CardViewDetailFontSize }" />
    <Setter Property="HorizontalOptions" Value="Start" />
    <Setter Property="Margin" Value="{ StaticResource CardViewDetailMargin }" />
</Style>

<Style TargetType="Image" x:Key="CardViewImageImageStyle">
    <Setter Property="HorizontalOptions" Value="Center" />
    <Setter Property="VerticalOptions" Value="Center" />
    <Setter Property="WidthRequest" Value="220"/>
    <Setter Property="HeightRequest" Value="165"/>
</Style>
```

#### <a name="4-create-the-control-layout-template"></a>4. créer le modèle de disposition de contrôle

La conception visuelle du contrôle personnalisé est déclarée explicitement dans le modèle de contrôle, à l’aide des ressources définies ci-dessus :

```xml
<!--- CARDVIEW -->
<ControlTemplate x:Key="CardViewControlControlTemplate">
  <StackLayout
    Spacing="0"
    BackgroundColor="{ TemplateBinding BackgroundColor }"
  >

    <!-- CARDVIEW IMAGE -->
    <Image
      Source="{ TemplateBinding ImageSource }"
      HorizontalOptions="FillAndExpand"
      VerticalOptions="StartAndExpand"
      Aspect="AspectFill"
      Style="{ StaticResource CardViewImageImageStyle }"
    />

    <!-- CARDVIEW TEXT -->
    <Label
      Text="{ TemplateBinding Text }"
      LineBreakMode="WordWrap"
      VerticalOptions="End"
      Style="{ StaticResource CardViewTextStyle }"
    />

    <!-- CARDVIEW DETAIL -->
    <Label
      Text="{ TemplateBinding Detail }"
      LineBreakMode="WordWrap"
      VerticalOptions="End"
      Style="{ StaticResource CardViewDetailStyle }" />

  </StackLayout>

</ControlTemplate>
```

#### <a name="5-add-the-theme-specific-resources"></a>5. ajouter les ressources spécifiques au thème

Étant donné qu’il s’agit d’un contrôle personnalisé, ajoutez les ressources qui correspondent au thème que vous utilisez dans le dictionnaire de ressources :

##### <a name="light-theme-colors"></a>Couleurs du thème clair

```xaml
<Color x:Key="iOSCardViewBackgroundColor">#FFFFFF</Color>
<Color x:Key="AndroidCardViewBackgroundColor">#FFFFFF</Color>

<Color x:Key="AndroidCardViewTextTextColor">#030303</Color>
<Color x:Key="iOSCardViewTextTextColor">#030303</Color>

<Color x:Key="AndroidCardViewDetailTextColor">#8F8E94</Color>
<Color x:Key="iOSCardViewDetailTextColor">#8F8E94</Color>
```

##### <a name="dark-theme-colors"></a>Couleurs de thème sombres

```xaml
<!-- CARD VIEW COLORS -->
            <Color x:Key="iOSCardViewBackgroundColor">#404040</Color>
            <Color x:Key="AndroidCardViewBackgroundColor">#404040</Color>

            <Color x:Key="AndroidCardViewTextTextColor">#FFFFFF</Color>
            <Color x:Key="iOSCardViewTextTextColor">#FFFFFF</Color>

            <Color x:Key="AndroidCardViewDetailTextColor">#B5B4B9</Color>
            <Color x:Key="iOSCardViewDetailTextColor">#B5B4B9</Color>
```

#### <a name="6-set-the-controltemplate-for-the-cardview-class"></a>6. définir le ControlTemplate pour la classe CardView

Enfin, assurez-vous que la classe C# créée à l' [étape 1](#1-dataview-subclass) utilise le modèle de contrôle défini à l' [étape 4](#4-create-the-control-layout-template) à l’aide d’un `Style` `Setter` élément.

```xml
<Style TargetType="local:CardView">
    <Setter Property="ControlTemplate" Value="{ StaticResource CardViewControlControlTemplate }" />
  ... some custom styling omitted
  <Setter Property="BackgroundColor" Value="{ StaticResource CardViewBackgroundColor }" />
</Style>
```

#### <a name="7-add-the-control-to-a-page"></a>7. ajouter le contrôle à une page

Le `CardView` contrôle peut désormais être ajouté à une page. L’exemple ci-dessous montre qu’il est hébergé dans un `StackLayout` :

```xaml
<StackLayout Spacing="0">
  <local:CardView
    Margin="12,6"
    ImageSource="{ DynamicResource CardViewImage }"
    Text="CardView Text"
    Detail="CardView Detail"
  />
</StackLayout>

```
