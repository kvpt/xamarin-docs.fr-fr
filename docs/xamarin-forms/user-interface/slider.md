---
title: Curseur de Xamarin.Forms
description: Le curseur de Xamarin.Forms est une barre horizontale qui peut être manipulée par l’utilisateur de sélectionner une valeur double à partir d’une plage continue. Cet article explique comment utiliser la classe de curseur pour sélectionner une valeur à partir d’une plage de valeurs continues.
ms.prod: xamarin
ms.assetid: 36B1C645-26E0-4874-B6B6-BDBF77662878
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/27/2019
ms.openlocfilehash: 1fa46af1ac1cf6ea49a53e3f8d3c3dca6ba83e13
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68511890"
---
# <a name="xamarinforms-slider"></a>Curseur de Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/SliderDemos)

_Utilisez un curseur de sélection à partir d’une plage de valeurs continues._

Xamarin.Forms [ `Slider` ](xref:Xamarin.Forms.Slider) est une barre horizontale qui peut être manipulée par l’utilisateur de sélectionner un `double` valeur à partir d’une plage continue.

Le `Slider` définit trois propriétés de type `double`:

- [`Minimum`](xref:Xamarin.Forms.Slider.Minimum) est la valeur minimale de la plage, avec une valeur par défaut 0.
- [`Maximum`](xref:Xamarin.Forms.Slider.Maximum) est la valeur maximale de la plage, avec une valeur par défaut de 1.
- [`Value`](xref:Xamarin.Forms.Slider.Value) est la valeur du curseur, qui peut être comprise entre `Minimum` et `Maximum` et a la valeur par défaut 0.

Tous les trois propriétés sont soutenues par `BindableProperty` objets. Le `Value` propriété a un mode de liaison par défaut de `BindingMode.TwoWay`, ce qui signifie qu’il est approprié comme source de liaison dans une application qui utilise le [Model-View-ViewModel (MVVM)](~/xamarin-forms/enterprise-application-patterns/mvvm.md) architecture.

> [!WARNING]
> En interne, le `Slider` garantit que `Minimum` est inférieure à `Maximum`. Si `Minimum` ou `Maximum` sont déjà définies afin que `Minimum` est non inférieur à `Maximum`, une exception est levée. Consultez le [ **précautions** ](#precautions) section ci-dessous pour plus d’informations sur la configuration de la `Minimum` et `Maximum` propriétés.

Le `Slider` force le `Value` propriété afin qu’il soit entre `Minimum` et `Maximum`, inclusif. Si le `Minimum` propriété est définie sur une valeur supérieure à la `Value` propriété, le `Slider` définit le `Value` propriété `Minimum`. De même, si `Maximum` est défini sur une valeur inférieure à `Value`, puis `Slider` définit le `Value` propriété `Maximum`.

`Slider` définit un [ `ValueChanged` ](xref:Xamarin.Forms.Slider.ValueChanged) événement est déclenché quand le `Value` modifications, soit par le biais de manipulation d’utilisateur le `Slider` ou lorsque le programme définit le `Value` propriété directement. Un `ValueChanged` événement est également déclenché quand le `Value` propriété est convertie comme décrit dans le paragraphe précédent.

Le [ `ValueChangedEventArgs` ](xref:Xamarin.Forms.ValueChangedEventArgs) objet qui accompagne le `ValueChanged` événement a deux propriétés, tous deux de type `double`: [ `OldValue` ](xref:Xamarin.Forms.ValueChangedEventArgs.OldValue) et [ `NewValue` ](xref:Xamarin.Forms.ValueChangedEventArgs.NewValue). Au moment où l’événement est déclenché, la valeur de `NewValue` est identique à la `Value` propriété de la `Slider` objet.

`Slider`définit `DragStarted` également les `DragCompleted` événements et, qui sont déclenchés au début et à la fin de l’action glisser. Contrairement à [`ValueChanged`](xref:Xamarin.Forms.Slider.ValueChanged) l’événement, `DragStarted` les `DragCompleted` événements et sont déclenchés uniquement via la manipulation `Slider`de l’utilisateur de. Lorsque l' `DragStarted` événement se déclenche, `DragStartedCommand`le, de `ICommand`type, est exécuté. De même, lorsque l' `DragCompleted` événement se déclenche, `DragCompletedCommand`le, de `ICommand`type, est exécuté.

> [!WARNING]
> N’utilisez pas les options de disposition horizontale sans contrainte de `Center`, `Start`, ou `End` avec `Slider`. Sur Android et UWP, le `Slider` est réduite à une barre de longueur nulle et sur iOS, la barre est très court. Conservez la valeur par défaut `HorizontalOptions` paramètre `Fill`et n’utilisez pas une largeur de `Auto` lors du placement de `Slider` dans un `Grid` mise en page.

Le `Slider` définit également plusieurs propriétés qui affectent son apparence :

- [`MinimumTrackColor`](xref:Xamarin.Forms.Slider.MinimumTrackColorProperty) est la barre de couleur sur le côté gauche du curseur de défilement.
- [`MaximumTrackColor`](xref:Xamarin.Forms.Slider.MaximumTrackColorProperty) est la barre de couleur située à droite du curseur de défilement.
- [`ThumbColor`](xref:Xamarin.Forms.Slider.ThumbColorProperty) est la couleur du curseur de défilement.
- [`ThumbImageSource`](xref:Xamarin.Forms.Slider.ThumbImageSourceProperty) est l’image à utiliser pour le thumb, de type [ `ImageSource` ](xref:Xamarin.Forms.ImageSource).

> [!NOTE]
> Le `ThumbColor` et `ThumbImageSource` propriétés s’excluent mutuellement. Si les deux propriétés sont définies, le `ThumbImageSource` propriété est prioritaire.

## <a name="basic-slider-code-and-markup"></a>Balisage et code de curseur de base

Le [ **SliderDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/SliderDemos) exemple commence par trois pages qui sont fonctionnellement identiques, mais sont implémentées de différentes façons. La première page utilise uniquement du code C#, la deuxième utilise XAML avec un gestionnaire d’événements dans le code et le troisième est possible d’éviter le Gestionnaire d’événements à l’aide de la liaison de données dans le fichier XAML.

### <a name="creating-a-slider-in-code"></a>Création d’un curseur dans le code

Le **base Code curseur** page dans le [ **SliderDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/SliderDemos) exemple montre comment créer un `Slider` et deux `Label` objets dans le code :

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

Le `Slider` est initialisé pour avoir un `Maximum` propriété 360. Le `ValueChanged` Gestionnaire de la `Slider` utilise le `Value` propriété de la `slider` objet à définir le `Rotation` propriété du premier `Label` et utilise le `String.Format` méthode avec le `NewValue` propriété de la arguments d’événement pour définir le `Text` propriété du deuxième `Label`. Ces deux approches pour obtenir la valeur actuelle de la `Slider` sont interchangeables.

Voici le programme en cours d’exécution sur iOS, Android et Universal Windows Platform (UWP) des appareils :

[![Code de base de curseur](slider-images/BasicSliderCode.png "Code de base de curseur")](slider-images/BasicSliderCode-Large.png#lightbox)

La seconde `Label` affiche le texte « (non initialisé) » jusqu'à ce que le `Slider` est manipulé, ce qui entraîne le premier `ValueChanged` événement à déclencher. Notez que le nombre de décimales affichées est différent pour chaque plateforme. Ces différences portent sur les implémentations de plateforme de la `Slider` et sont décrits plus loin dans cet article dans la section [différences d’implémentation de plateforme](#implementations).

### <a name="creating-a-slider-in-xaml"></a>Création d’un curseur dans XAML

Le **base XAML de curseur** page est fonctionnellement identique **base Code curseur** mais implémentée principalement dans XAML :

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

Le fichier code-behind contient le gestionnaire pour le `ValueChanged` événement :

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

Il est également possible pour le Gestionnaire d’événements obtenir le `Slider` qui déclenche l’événement via le `sender` argument. Le `Value` propriété contient la valeur actuelle :

```csharp
double value = ((Slider)sender).Value;
```

Si le `Slider` objet ont été donnés un nom dans le fichier XAML avec un `x:Name` attribut (par exemple, « curseur »), puis le Gestionnaire d’événements peut référencer cet objet directement :

```csharp
double value = slider.Value;
```

### <a name="data-binding-the-slider"></a>Le curseur de liaison de données

Le **des liaisons de curseur base** page montre comment écrire un programme presque équivalent qui élimine le `Value` Gestionnaire d’événements à l’aide de [une liaison de données](~/xamarin-forms/app-fundamentals/data-binding/index.md):

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

Le `Rotation` propriété du premier `Label` est lié à la `Value` propriété de la `Slider`, comme est le `Text` propriété du deuxième `Label` avec un `StringFormat` spécification. La page des **liaisons de curseur de base** fonctionne un peu différemment des deux pages précédentes: Lorsque la page s’affiche pour la première `Label` fois, la deuxième affiche la chaîne de texte avec la valeur. Il s’agit d’un avantage de l’utilisation de la liaison de données. Pour afficher un texte sans liaison de données, vous devez initialiser spécifiquement le `Text` propriété de la `Label` ou simuler le déclenchement de le `ValueChanged` événement en appelant le Gestionnaire d’événements à partir du constructeur de classe.

<a name="precautions" />

## <a name="precautions"></a>Précautions

La valeur de la `Minimum` propriété doit toujours être inférieure à la valeur de la `Maximum` propriété. Le code suivant extrait de code entraîne la `Slider` pour lever une exception :

```csharp
// Throws an exception!
Slider slider = new Slider
{
    Minimum = 10,
    Maximum = 20
};
```

Le compilateur C# génère du code qui définit ces deux propriétés dans la séquence, et à quel moment le `Minimum` propriété est définie sur 10, il est supérieur à la valeur par défaut `Maximum` la valeur 1. Vous pouvez éviter l’exception dans ce cas en définissant le `Maximum` propriété premier :

```csharp
Slider slider = new Slider
{
    Maximum = 20,
    Minimum = 10
};
```

Paramètre `Maximum` à 20 n’est pas un problème, car il est supérieur à la valeur par défaut `Minimum` la valeur 0. Lorsque `Minimum` est défini, la valeur est inférieure à la `Maximum` valeur de 20.

Le même problème se produit dans XAML. Définir les propriétés dans un ordre qui garantit que `Maximum` est toujours supérieure à `Minimum`:

```xaml
<Slider Maximum="20"
        Minimum="10" ... />
```

Vous pouvez définir le `Minimum` et `Maximum` des valeurs pour les nombres négatifs, mais uniquement dans un ordre dans lequel `Minimum` est toujours inférieure à `Maximum`:

```xaml
<Slider Minimum="-20"
        Maximum="-10" ... />
```

Le `Value` propriété est toujours supérieure ou égale à la `Minimum` valeur et inférieure ou égale à `Maximum`. Si `Value` est défini sur une valeur en dehors de cette plage, la valeur sera être convertie pour être comprises entre, mais aucune exception n’est levée. Par exemple, ce code vous sera *pas* lever une exception :

```csharp
Slider slider = new Slider
{
    Value = 10
};
```

Au lieu de cela, le `Value` propriété est convertie en la `Maximum` la valeur 1.

Voici un extrait de code ci-dessus :

```csharp
Slider slider = new Slider
{
    Maximum = 20,
    Minimum = 10
};
```

Lorsque `Minimum` a la valeur 10, puis `Value` est également défini sur 10.

Si un `ValueChanged` Gestionnaire d’événements a été attaché au moment qui le `Value` propriété est forcée sur autre chose que sa valeur par défaut de 0, puis un `ValueChanged` événement est déclenché. Voici un extrait du XAML :

```xaml
<Slider ValueChanged="OnSliderValueChanged"
        Maximum="20"
        Minimum="10" />
```

Lorsque `Minimum` est définie sur 10, `Value` est également défini sur 10 et le `ValueChanged` événement est déclenché. Cela peut se produire avant que le reste de la page a été construit, et le gestionnaire peut tenter de référencer d’autres éléments sur la page qui n’ont pas encore été créées. Vous souhaiterez peut-être ajouter du code pour le `ValueChanged` gestionnaire vérifie pour `null` les valeurs des autres éléments sur la page. Ou, vous pouvez définir le `ValueChanged` Gestionnaire d’événements après le `Slider` valeurs ont été initialisés.

<a name="implementations" />

## <a name="platform-implementation-differences"></a>Différences d’implémentation de plateforme

Les captures d’écran illustrés précédemment affichent la valeur de la `Slider` avec un nombre différent de la virgule décimale. Ceci est lié à la manière dont le `Slider` est implémentée sur les plateformes Android et UWP.

### <a name="the-android-implementation"></a>L’implémentation Android

L’implémentation Android de `Slider` repose sur Android [ `SeekBar` ](xref:Android.Widget.SeekBar) et définit toujours la [ `Max` ](xref:Android.Widget.ProgressBar.Max) propriété la valeur 1000. Cela signifie que le `Slider` sur Android a des valeurs discrètes uniquement 1 001. Si vous définissez la `Slider` pour avoir un `Minimum` de 0 et un `Maximum` de 5000, puis en tant que le `Slider` est manipulé, le `Value` propriété a les valeurs 0, 5, 10, 15 et ainsi de suite.

### <a name="the-uwp-implementation"></a>L’implémentation UWP

L’implémentation UWP de `Slider` repose sur la plateforme Windows universelle [ `Slider` ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.slider) contrôle. Le `StepFrequency` propriété de la plateforme Windows universelle `Slider` est défini sur la différence entre la `Maximum` et `Minimum` propriétés divisé par 10, mais pas supérieure à 1.

Par exemple, pour la plage par défaut de 0 à 1, le `StepFrequency` propriété a la valeur 0.1. Comme le `Slider` est manipulé, le `Value` propriété est limitée à 0, 0.1, 0.2, 0.3, 0,4, 0,5, 0.6, 0,7, 0,8, 0,9 et 1.0. (Ceci est évident dans la dernière page dans le [ **SliderDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/SliderDemos) exemple.) Lorsque la différence entre la `Maximum` et `Minimum` propriétés est 10 ou version ultérieure, puis `StepFrequency` est défini sur 1 et le `Value` propriété a des valeurs intégrales.

### <a name="the-stepslider-solution"></a>La solution StepSlider

Plus polyvalents `StepSlider` est abordée dans [chapitre 27. Renderers personnalisés](https://xamarin.azureedge.net/developer/xamarin-forms-book/XamarinFormsBook-Ch27-Apr2016.pdf) du livre *création d’applications mobiles avec Xamarin.Forms*. Le `StepSlider` est similaire à `Slider` mais ajoute un `Steps` propriété pour spécifier le nombre de valeurs comprises entre `Minimum` et `Maximum`.

## <a name="sliders-for-color-selection"></a>Curseurs de sélection de couleur

La dernière des deux pages dans le [ **SliderDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/SliderDemos) exemple utilisent tous deux trois `Slider` instances pour la sélection de couleur. La première page gère toutes les interactions dans le fichier code-behind, tandis que la deuxième page montre comment utiliser la liaison de données avec un ViewModel.

### <a name="handling-sliders-in-the-code-behind-file"></a>Gestion des curseurs dans le fichier code-behind

Le **curseurs de couleur RVB** page instancie un `BoxView` pour afficher une couleur, trois `Slider` instances pour sélectionner les composants rouges, vert et bleus de la couleur et trois `Label` éléments d’affichage de ces couleur valeurs :

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

Un `Style` donne les trois `Slider` éléments une plage de 0 à 255. Le `Slider` éléments partagent le même `ValueChanged` gestionnaire, qui est implémentée dans le fichier code-behind :

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

Les premiers jeux de section la `Text` propriété d’un de la `Label` instances pour une courte chaîne de texte indiquant la valeur de la `Slider` au format hexadécimal. Ensuite, les trois `Slider` instances sont accessibles pour créer un `Color` valeur à partir des composants RVB :

[![Les curseurs de couleur RVB](slider-images/RgbColorSliders.png "curseurs de couleur RVB")](slider-images/RgbColorSliders-Large.png#lightbox)

### <a name="binding-the-slider-to-a-viewmodel"></a>Le curseur de la liaison à un ViewModel

Le **curseurs de couleurs TSL** page montre comment utiliser un ViewModel pour effectuer les calculs utilisés pour créer un `Color` valeur à partir des valeurs de teinte, saturation et de luminosité. Comme tous les ViewModels, le `HSLColorViewModel` la classe implémente le `INotifyPropertyChanged` interface et déclenche une `PropertyChanged` événement chaque fois qu’une des propriétés change :

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

ViewModels et `INotifyPropertyChanged` interface sont décrites dans l’article [une liaison de données](~/xamarin-forms/app-fundamentals/data-binding/index.md).

Le **HslColorSlidersPage.xaml** fichier instancie le `HslColorViewModel` et lui affecte la valeur de la page `BindingContext` propriété. Ainsi, tous les éléments dans le fichier XAML pour lier aux propriétés dans le ViewModel :

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

Comme le `Slider` éléments sont manipulées, le `BoxView` et `Label` éléments sont mis à jour à partir du ViewModel :

[![Les curseurs de couleurs TSL](slider-images/HslColorSliders.png "curseurs de couleurs TSL")](slider-images/HslColorSliders-Large.png#lightbox)

Le `StringFormat` composant de la `Binding` extension de balisage est définie pour un format de « F2 » pour afficher deux décimales. (Chaîne de mise en forme dans les liaisons de données est décrite dans l’article [mise en forme de chaîne](~/xamarin-forms/app-fundamentals/data-binding/string-formatting.md).) Toutefois, la version UWP du programme est limitée aux valeurs de 0, 0.1, 0.2... 0,9 et 1,0. Il s’agit d’une conséquence directe de l’implémentation de la plateforme Windows universelle `Slider` comme décrit ci-dessus dans la section [différences d’implémentation de plateforme](#implementations).

## <a name="related-links"></a>Liens associés

- [Exemple de démonstrations de curseur](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/SliderDemos)
- [Curseur API](xref:Xamarin.Forms.Slider)
