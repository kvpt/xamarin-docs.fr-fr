---
title: Xamarin.Forms Curseur
description: Le Xamarin.Forms curseur est une barre horizontale qui peut être manipulée par l’utilisateur pour sélectionner une valeur double dans une plage continue. Cet article explique comment utiliser la classe Slider pour sélectionner une valeur dans une plage de valeurs continues.
ms.prod: xamarin
ms.assetid: 36B1C645-26E0-4874-B6B6-BDBF77662878
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/27/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 364cda6372986113e8a782a061783e0ca5455f3b
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93368542"
---
# <a name="no-locxamarinforms-slider"></a>Xamarin.Forms Curseur

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](/samples/xamarin/xamarin-forms-samples/userinterface-sliderdemos)

_Utilisez un curseur pour la sélection à partir d’une plage de valeurs continues._

Xamarin.Forms [`Slider`](xref:Xamarin.Forms.Slider) Est une barre horizontale qui peut être manipulée par l’utilisateur pour sélectionner une `double` valeur dans une plage continue.

`Slider`Définit trois propriétés de type `double` :

- [`Minimum`](xref:Xamarin.Forms.Slider.Minimum) est le minimum de la plage, avec 0 comme valeur par défaut.
- [`Maximum`](xref:Xamarin.Forms.Slider.Maximum) correspond au maximum de la plage, avec 1 comme valeur par défaut.
- [`Value`](xref:Xamarin.Forms.Slider.Value) est la valeur du curseur, qui peut être comprise entre `Minimum` et `Maximum` et a une valeur par défaut de 0.

Les trois propriétés sont sauvegardées par des `BindableProperty` objets. La `Value` propriété a un mode de liaison par défaut de, ce qui `BindingMode.TwoWay` signifie qu’elle est appropriée comme source de liaison dans une application qui utilise l’architecture [MVVM (Model-View-ViewModel)](~/xamarin-forms/enterprise-application-patterns/mvvm.md) .

> [!WARNING]
> En interne, la `Slider` s’assure que `Minimum` est inférieur à `Maximum` . Si `Minimum` ou `Maximum` sont toujours définis de sorte que `Minimum` n’est pas inférieur à `Maximum` , une exception est levée. Pour plus d’informations sur la définition des propriétés et, consultez la section [**précautions**](#precautions) ci-dessous `Minimum` `Maximum` .

Le `Slider` force la `Value` propriété afin qu’elle soit comprise entre `Minimum` et `Maximum` , inclus. Si la `Minimum` propriété est définie sur une valeur supérieure à la `Value` propriété, la `Slider` propriété affecte la valeur `Value` à la propriété `Minimum` . De même, si `Maximum` a une valeur inférieure à `Value` , `Slider` affecte à la propriété la valeur `Value` `Maximum` .

`Slider` définit un [`ValueChanged`](xref:Xamarin.Forms.Slider.ValueChanged) événement qui est déclenché lorsque `Value` change, soit par manipulation de l’utilisateur du `Slider` ou lorsque le programme définit la `Value` propriété directement. Un `ValueChanged` événement est également déclenché lorsque la `Value` propriété est forcée, comme décrit dans le paragraphe précédent.

L' [`ValueChangedEventArgs`](xref:Xamarin.Forms.ValueChangedEventArgs) objet qui accompagne l' `ValueChanged` événement a deux propriétés, de type `double` : [`OldValue`](xref:Xamarin.Forms.ValueChangedEventArgs.OldValue) et [`NewValue`](xref:Xamarin.Forms.ValueChangedEventArgs.NewValue) . Au moment où l’événement est déclenché, la valeur de `NewValue` est identique à la `Value` propriété de l' `Slider` objet.

`Slider` définit également `DragStarted` les `DragCompleted` événements et, qui sont déclenchés au début et à la fin de l’action glisser. Contrairement [`ValueChanged`](xref:Xamarin.Forms.Slider.ValueChanged) à l’événement, `DragStarted` les `DragCompleted` événements et sont déclenchés uniquement via la manipulation de l’utilisateur de `Slider` . Lorsque l' `DragStarted` événement se déclenche, le `DragStartedCommand` , de type `ICommand` , est exécuté. De même, lorsque l' `DragCompleted` événement se déclenche, le `DragCompletedCommand` , de type `ICommand` , est exécuté.

> [!WARNING]
> N’utilisez pas les options de disposition horizontale sans contrainte de `Center` , `Start` ou `End` avec `Slider` . Sur Android et UWP, le `Slider` réduit à une barre de zéro longueur et, sur iOS, la barre est très brève. Conservez le `HorizontalOptions` paramètre par défaut `Fill` et n’utilisez pas une largeur `Auto` pour la `Slider` mise en `Grid` page.

`Slider`Définit également plusieurs propriétés qui affectent son apparence :

- [`MinimumTrackColor`](xref:Xamarin.Forms.Slider.MinimumTrackColorProperty) couleur de la barre située à gauche du curseur.
- [`MaximumTrackColor`](xref:Xamarin.Forms.Slider.MaximumTrackColorProperty) couleur de la barre située à droite du curseur.
- [`ThumbColor`](xref:Xamarin.Forms.Slider.ThumbColorProperty) est la couleur de curseur.
- [`ThumbImageSource`](xref:Xamarin.Forms.Slider.ThumbImageSourceProperty) image à utiliser pour le curseur de type [`ImageSource`](xref:Xamarin.Forms.ImageSource) .

> [!NOTE]
> Les `ThumbColor` Propriétés et s’excluent `ThumbImageSource` mutuellement. Si les deux propriétés sont définies, la `ThumbImageSource` propriété est prioritaire.

## <a name="basic-slider-code-and-markup"></a>Code et balisage de base du curseur

L’exemple [**SliderDemos**](/samples/xamarin/xamarin-forms-samples/userinterface-sliderdemos) commence par trois pages fonctionnellement identiques, mais elles sont implémentées de différentes façons. La première page utilise uniquement du code C#, la seconde utilise XAML avec un gestionnaire d’événements dans le code, et la troisième est en mesure d’éviter le gestionnaire d’événements à l’aide de la liaison de données dans le fichier XAML.

### <a name="creating-a-slider-in-code"></a>Création d’un curseur dans le code

La page de **codes du curseur de base** dans l’exemple [**SliderDemos**](/samples/xamarin/xamarin-forms-samples/userinterface-sliderdemos) montre comment créer un `Slider` et deux `Label` objets dans le code :

```csharp
public class BasicSliderCodePage : ContentPage
{
    public BasicSliderCodePage()
    {
        Label rotationLabel = new Label
        {
            Text = "ROTATING TEXT",
            FontSize = Device.GetNamedSize(NamedSize.Large, typeof(Label)),
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.CenterAndExpand
        };

        Label displayLabel = new Label
        {
            Text = "(uninitialized)",
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.CenterAndExpand
        };

        Slider slider = new Slider
        {
            Maximum = 360
        };
        slider.ValueChanged += (sender, args) =>
        {
            rotationLabel.Rotation = slider.Value;
            displayLabel.Text = String.Format("The Slider value is {0}", args.NewValue);
        };

        Title = "Basic Slider Code";
        Padding = new Thickness(10, 0);
        Content = new StackLayout
        {
            Children =
            {
                rotationLabel,
                slider,
                displayLabel
            }
        };
    }
}
```

`Slider`Est initialisé pour avoir une `Maximum` propriété de 360. Le `ValueChanged` Gestionnaire du `Slider` utilise la `Value` propriété de l' `slider` objet pour définir la `Rotation` propriété du premier `Label` et utilise la `String.Format` méthode avec la `NewValue` propriété des arguments d’événement pour définir la `Text` propriété du deuxième `Label` . Ces deux approches pour obtenir la valeur actuelle de `Slider` sont interchangeables.

Voici le programme en cours d’exécution sur les appareils iOS et Android :

[![Code Slider de base](slider-images/BasicSliderCode.png "Code Slider de base")](slider-images/BasicSliderCode-Large.png#lightbox)

Le deuxième `Label` affiche le texte « (non initialisé) » jusqu’à ce que le `Slider` soit manipulé, ce qui entraîne le déclenchement du premier `ValueChanged` événement. Notez que le nombre de décimales affichées est différent pour chaque plateforme. Ces différences sont liées aux implémentations de plateforme du `Slider` et sont abordées plus loin dans cet article dans la section [différences d’implémentation](#platform-implementation-differences)de la plateforme.

### <a name="creating-a-slider-in-xaml"></a>Création d’un curseur en XAML

La page **XAML de base du curseur** est fonctionnellement identique au **Code de curseur de base** , mais implémentée principalement en XAML :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="SliderDemos.BasicSliderXamlPage"
             Title="Basic Slider XAML"
             Padding="10, 0">
    <StackLayout>
        <Label x:Name="rotatingLabel"
               Text="ROTATING TEXT"
               FontSize="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <Slider Maximum="360"
                ValueChanged="OnSliderValueChanged" />

        <Label x:Name="displayLabel"
               Text="(uninitialized)"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

Le fichier code-behind contient le gestionnaire de l' `ValueChanged` événement :

```csharp
public partial class BasicSliderXamlPage : ContentPage
{
    public BasicSliderXamlPage()
    {
        InitializeComponent();
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        double value = args.NewValue;
        rotatingLabel.Rotation = value;
        displayLabel.Text = String.Format("The Slider value is {0}", value);
    }
}
```

Il est également possible que le gestionnaire d’événements obtienne le `Slider` qui déclenche l’événement à l’aide de l' `sender` argument. La `Value` propriété contient la valeur actuelle :

```csharp
double value = ((Slider)sender).Value;
```

Si `Slider` un nom est attribué à l’objet dans le fichier XAML avec un `x:Name` attribut (par exemple, « Slider »), le gestionnaire d’événements peut référencer directement cet objet :

```csharp
double value = slider.Value;
```

### <a name="data-binding-the-slider"></a>Liaison de données du curseur

La page des **liaisons de curseur de base** montre comment écrire un programme quasiment équivalent qui élimine le `Value` Gestionnaire d’événements à l’aide de la liaison de [données](~/xamarin-forms/app-fundamentals/data-binding/index.md):

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="SliderDemos.BasicSliderBindingsPage"
             Title="Basic Slider Bindings"
             Padding="10, 0">
    <StackLayout>
        <Label Text="ROTATING TEXT"
               Rotation="{Binding Source={x:Reference slider},
                                  Path=Value}"
               FontSize="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <Slider x:Name="slider"
                Maximum="360" />

        <Label x:Name="displayLabel"
               Text="{Binding Source={x:Reference slider},
                              Path=Value,
                              StringFormat='The Slider value is {0:F0}'}"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

La `Rotation` propriété du premier `Label` est liée à la `Value` propriété de `Slider` , tout comme la `Text` propriété de la seconde `Label` avec une `StringFormat` spécification. La page des **liaisons de curseur de base** fonctionne un peu différemment des deux pages précédentes : lorsque la page s’affiche pour la première fois, la deuxième `Label` affiche la chaîne de texte avec la valeur. Il s’agit d’un avantage de l’utilisation de la liaison de données. Pour afficher du texte sans liaison de données, vous devez initialiser spécifiquement la `Text` propriété de `Label` ou simuler un déclenchement de l' `ValueChanged` événement en appelant le gestionnaire d’événements à partir du constructeur de classe.

## <a name="precautions"></a>Précautions

La valeur de la `Minimum` propriété doit toujours être inférieure à la valeur de la `Maximum` propriété. L’extrait de code suivant provoque la `Slider` levée d’une exception par le.

```csharp
// Throws an exception!
Slider slider = new Slider
{
    Minimum = 10,
    Maximum = 20
};
```

Le compilateur C# génère du code qui définit ces deux propriétés en séquence et, lorsque la `Minimum` propriété a la valeur 10, elle est supérieure à la `Maximum` valeur par défaut 1. Dans ce cas, vous pouvez éviter l’exception en définissant la `Maximum` propriété en premier :

```csharp
Slider slider = new Slider
{
    Maximum = 20,
    Minimum = 10
};
```

Le paramétrage `Maximum` de la valeur 20 n’est pas un problème, car il est supérieur à la valeur par défaut `Minimum` 0. Lorsque `Minimum` est défini, la valeur est inférieure à la `Maximum` valeur 20.

Le même problème existe dans XAML. Définissez les propriétés dans un ordre qui garantit que `Maximum` est toujours supérieur à `Minimum` :

```xaml
<Slider Maximum="20"
        Minimum="10" ... />
```

Vous pouvez définir les `Minimum` `Maximum` valeurs et sur des nombres négatifs, mais uniquement dans un ordre où `Minimum` est toujours inférieur à `Maximum` :

```xaml
<Slider Minimum="-20"
        Maximum="-10" ... />
```

La `Value` propriété est toujours supérieure ou égale à la `Minimum` valeur et inférieure ou égale à `Maximum` . Si `Value` est défini sur une valeur en dehors de cette plage, la valeur sera forcée à se trouver dans la plage, mais aucune exception n’est levée. Par exemple, ce code ne lève *pas* d’exception :

```csharp
Slider slider = new Slider
{
    Value = 10
};
```

Au lieu de cela, la `Value` propriété est forcée à la `Maximum` valeur 1.

Voici un extrait de code ci-dessus :

```csharp
Slider slider = new Slider
{
    Maximum = 20,
    Minimum = 10
};
```

Lorsque `Minimum` a la valeur 10, `Value` est également défini sur 10.

Si un `ValueChanged` Gestionnaire d’événements a été attaché au moment où la `Value` propriété est forcée à une valeur autre que sa valeur par défaut 0, un `ValueChanged` événement est déclenché. Voici un extrait de code XAML :

```xaml
<Slider ValueChanged="OnSliderValueChanged"
        Maximum="20"
        Minimum="10" />
```

Lorsque `Minimum` a la valeur 10, `Value` est également défini sur 10 et l' `ValueChanged` événement est déclenché. Cela peut se produire avant la construction du reste de la page, et le gestionnaire peut tenter de faire référence à d’autres éléments de la page qui n’ont pas encore été créés. Vous souhaiterez peut-être ajouter du code au `ValueChanged` gestionnaire qui vérifie les `null` valeurs d’autres éléments sur la page. Ou bien, vous pouvez définir le `ValueChanged` Gestionnaire d’événements une fois que les `Slider` valeurs ont été initialisées.

## <a name="platform-implementation-differences"></a>Différences d’implémentation de plateforme

Les captures d’écran indiquées précédemment affichent la valeur de `Slider` avec un nombre différent de points décimaux. Cela concerne la façon dont le `Slider` est implémenté sur les plateformes Android et UWP.

### <a name="the-android-implementation"></a>Implémentation Android

L’implémentation Android de `Slider` est basée sur Android [`SeekBar`](xref:Android.Widget.SeekBar) et affecte toujours la valeur [`Max`](xref:Android.Widget.ProgressBar.Max) 1000 à la propriété. Cela signifie que le `Slider` sur Android a seulement 1 001 valeurs discrètes. Si vous affectez `Slider` à la valeur `Minimum` 0 et un `Maximum` de 5000, alors que `Slider` est manipulé, la `Value` propriété a les valeurs 0, 5, 10, 15, et ainsi de suite.

### <a name="the-uwp-implementation"></a>Implémentation UWP

L’implémentation UWP de `Slider` est basée sur le [`Slider`](/uwp/api/windows.ui.xaml.controls.slider) contrôle UWP. La `StepFrequency` propriété du UWP `Slider` est définie sur la différence des `Maximum` `Minimum` Propriétés et divisées par 10, mais pas supérieure à 1.

Par exemple, pour la plage par défaut de 0 à 1, la `StepFrequency` propriété est définie sur 0,1. Lorsque `Slider` est manipulé, la `Value` propriété est limitée à 0, 0,1, 0,2, 0,3, 0,4, 0,5, 0,6, 0,7, 0,8, 0,9 et 1,0. (Cela est évident dans la dernière page de l’exemple [**SliderDemos**](/samples/xamarin/xamarin-forms-samples/userinterface-sliderdemos) .) Lorsque la différence entre les `Maximum` `Minimum` Propriétés et est supérieure ou égale à 10, `StepFrequency` a la valeur 1 et la `Value` propriété a des valeurs intégrales.

### <a name="the-stepslider-solution"></a>La solution StepSlider

Un plus polyvalent `StepSlider` est abordé dans le [chapitre 27. Les convertisseurs personnalisés](https://xamarin.azureedge.net/developer/xamarin-forms-book/XamarinFormsBook-Ch27-Apr2016.pdf) du livre *qui créent des Xamarin.Forms Mobile Apps avec*. `StepSlider`Est semblable à `Slider` mais ajoute une `Steps` propriété pour spécifier le nombre de valeurs entre `Minimum` et `Maximum` .

## <a name="sliders-for-color-selection"></a>Curseurs pour la sélection des couleurs

Les deux dernières pages de l’exemple [**SliderDemos**](/samples/xamarin/xamarin-forms-samples/userinterface-sliderdemos) utilisent toutes les deux `Slider` des trois instances pour la sélection de couleurs. La première page gère toutes les interactions dans le fichier code-behind, tandis que la deuxième page montre comment utiliser la liaison de données avec un ViewModel.

### <a name="handling-sliders-in-the-code-behind-file"></a>Gestion des curseurs dans le fichier code-behind

La page **curseurs de couleur RVB** instancie un `BoxView` pour afficher une couleur, trois `Slider` instances pour sélectionner les composants rouge, vert et bleu de la couleur et trois `Label` éléments pour afficher ces valeurs de couleur :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="SliderDemos.RgbColorSlidersPage"
             Title="RGB Color Sliders">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Slider">
                <Setter Property="Maximum" Value="255" />
            </Style>

            <Style TargetType="Label">
                <Setter Property="HorizontalTextAlignment" Value="Center" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout Margin="10">
        <BoxView x:Name="boxView"
                 Color="Black"
                 VerticalOptions="FillAndExpand" />

        <Slider x:Name="redSlider"
                ValueChanged="OnSliderValueChanged" />

        <Label x:Name="redLabel" />

        <Slider x:Name="greenSlider"
                ValueChanged="OnSliderValueChanged" />

        <Label x:Name="greenLabel" />

        <Slider x:Name="blueSlider"
                ValueChanged="OnSliderValueChanged" />

        <Label x:Name="blueLabel" />
    </StackLayout>
</ContentPage>
```

Un `Style` donne aux trois `Slider` éléments une plage comprise entre 0 et 255. Les `Slider` éléments partagent le même `ValueChanged` Gestionnaire, qui est implémenté dans le fichier code-behind :

```csharp
public partial class RgbColorSlidersPage : ContentPage
{
    public RgbColorSlidersPage()
    {
        InitializeComponent();
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        if (sender == redSlider)
        {
            redLabel.Text = String.Format("Red = {0:X2}", (int)args.NewValue);
        }
        else if (sender == greenSlider)
        {
            greenLabel.Text = String.Format("Green = {0:X2}", (int)args.NewValue);
        }
        else if (sender == blueSlider)
        {
            blueLabel.Text = String.Format("Blue = {0:X2}", (int)args.NewValue);
        }

        boxView.Color = Color.FromRgb((int)redSlider.Value,
                                      (int)greenSlider.Value,
                                      (int)blueSlider.Value);
    }
}
```

La première section définit la `Text` propriété de l’une des `Label` instances sur une chaîne de texte brève indiquant la valeur de l' `Slider` hexadécimal. Ensuite, les trois `Slider` instances sont accessibles pour créer une `Color` valeur à partir des composants RGB :

[![Curseurs de couleur RVB](slider-images/RgbColorSliders.png "Curseurs de couleur RVB")](slider-images/RgbColorSliders-Large.png#lightbox)

### <a name="binding-the-slider-to-a-viewmodel"></a>Liaison du curseur à un ViewModel

La page **curseurs de couleurs TSL** montre comment utiliser un ViewModel pour effectuer les calculs utilisés pour créer une `Color` valeur à partir des valeurs de teinte, de saturation et de luminosité. Comme tous les ViewModels, la `HSLColorViewModel` classe implémente l' `INotifyPropertyChanged` interface et déclenche un `PropertyChanged` événement chaque fois qu’une des propriétés change :

```csharp
public class HslColorViewModel : INotifyPropertyChanged
{
    Color color;

    public event PropertyChangedEventHandler PropertyChanged;

    public double Hue
    {
        set
        {
            if (color.Hue != value)
            {
                Color = Color.FromHsla(value, color.Saturation, color.Luminosity);
            }
        }
        get
        {
            return color.Hue;
        }
    }

    public double Saturation
    {
        set
        {
            if (color.Saturation != value)
            {
                Color = Color.FromHsla(color.Hue, value, color.Luminosity);
            }
        }
        get
        {
            return color.Saturation;
        }
    }

    public double Luminosity
    {
        set
        {
            if (color.Luminosity != value)
            {
                Color = Color.FromHsla(color.Hue, color.Saturation, value);
            }
        }
        get
        {
            return color.Luminosity;
        }
    }

    public Color Color
    {
        set
        {
            if (color != value)
            {
                color = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Hue"));
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Saturation"));
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Luminosity"));
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Color"));
            }
        }
        get
        {
            return color;
        }
    }
}
```

ViewModels et l' `INotifyPropertyChanged` interface sont abordés dans l’article [liaison de données](~/xamarin-forms/app-fundamentals/data-binding/index.md).

Le fichier **HslColorSlidersPage. Xaml** instancie le `HslColorViewModel` et le définit sur la propriété de la page `BindingContext` . Cela permet à tous les éléments du fichier XAML d’être liés aux propriétés dans le ViewModel :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:SliderDemos"
             x:Class="SliderDemos.HslColorSlidersPage"
             Title="HSL Color Sliders">

    <ContentPage.BindingContext>
        <local:HslColorViewModel Color="Chocolate" />
    </ContentPage.BindingContext>

    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Label">
                <Setter Property="HorizontalTextAlignment" Value="Center" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout Margin="10">
        <BoxView Color="{Binding Color}"
                 VerticalOptions="FillAndExpand" />

        <Slider Value="{Binding Hue}" />
        <Label Text="{Binding Hue, StringFormat='Hue = {0:F2}'}" />

        <Slider Value="{Binding Saturation}" />
        <Label Text="{Binding Saturation, StringFormat='Saturation = {0:F2}'}" />

        <Slider Value="{Binding Luminosity}" />
        <Label Text="{Binding Luminosity, StringFormat='Luminosity = {0:F2}'}" />
    </StackLayout>
</ContentPage>
```

À mesure que les `Slider` éléments sont manipulés, les `BoxView` `Label` éléments et sont mis à jour à partir du ViewModel :

[![Curseurs de couleurs TSL](slider-images/HslColorSliders.png "Curseurs de couleurs TSL")](slider-images/HslColorSliders-Large.png#lightbox)

Le `StringFormat` composant de l' `Binding` extension de balisage est défini pour un format « F2 » afin d’afficher deux décimales. (La mise en forme des chaînes dans les liaisons de données est décrite dans l’article [mise en forme de chaîne](~/xamarin-forms/app-fundamentals/data-binding/string-formatting.md).) Toutefois, la version UWP du programme est limitée aux valeurs 0, 0,1, 0,2,... 0,9 et 1,0. Il s’agit d’un résultat direct de l’implémentation de la série UWP `Slider` , comme décrit ci-dessus dans la section différences de l’implémentation de la [plateforme](#platform-implementation-differences).

## <a name="related-links"></a>Liens associés

- [Exemples de démonstrations Slider](/samples/xamarin/xamarin-forms-samples/userinterface-sliderdemos)
- [API Slider](xref:Xamarin.Forms.Slider)