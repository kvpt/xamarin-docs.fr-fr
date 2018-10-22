---
title: Bouton de Xamarin.Forms
description: Le bouton répond à un drainage ou d’un clic qui dirige une application pour effectuer une tâche particulière.
ms.prod: xamarin
ms.assetid: 62CAEB63-0800-44F4-9B8C-EE632138C2F5
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 06/01/2018
ms.openlocfilehash: d74e0b2aa5be6e8eee2ce5cb54572dd4113d4d7d
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35244914"
---
# <a name="xamarinforms-button"></a>Bouton de Xamarin.Forms

_Le bouton répond à un drainage ou d’un clic qui dirige une application pour effectuer une tâche particulière._

Le [ `Button` ](xref:Xamarin.Forms.Button) est le plus fondamental contrôle interactif dans tous les Xamarin.Forms. Le `Button` généralement affiche une chaîne de texte courte qui indique une commande, mais il peut également afficher une image bitmap, ou une combinaison de texte et une image. L’utilisateur appuie sur le `Button` avec un doigt ou clique dessus avec la souris pour exécuter cette commande.

La plupart des rubriques ci-dessous correspondre aux pages dans le [ **ButtonDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ButtonDemos) exemple.

## <a name="handling-button-clicks"></a>Gestion bouton clique sur

`Button` définit un [ `Clicked` ](xref:Xamarin.Forms.Button.Clicked) événement est déclenché lorsque l’utilisateur clique sur le `Button` avec un pointeur de souris ou du doigt. L’événement est déclenché lorsque le bouton doigt ou de la souris est relâché à partir de la surface de la `Button`. Le `Button` doit avoir son [ `IsEnabled` ](xref:Xamarin.Forms.VisualElement.IsEnabled) propriété `true` pour pouvoir répondre aux clics.

Le **base Click du bouton** page dans le [ **ButtonDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ButtonDemos) exemple montre comment instancier un `Button` dans XAML et de gérer ses `Clicked` événement. Le **BasicButtonClickPage.xaml** fichier contient un `StackLayout` à la fois avec un `Label` et un `Button`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ButtonDemos.BasicButtonClickPage"
             Title="Basic Button Click">
    <StackLayout>

        <Label x:Name="label"
               Text="Click the Button below"
               FontSize="Large"
               VerticalOptions="CenterAndExpand"
               HorizontalOptions="Center" />

        <Button Text="Click to Rotate Text!"
                VerticalOptions="CenterAndExpand"
                HorizontalOptions="Center"
                Clicked="OnButtonClicked" />

    </StackLayout>
</ContentPage>
```

Le `Button` a tendance à occuper tout l’espace autorisé pour elle. Par exemple, si vous ne définissez pas le `HorizontalOptions` propriété du `Button` une valeur autre que `Fill`, le `Button` occupe toute la largeur de son parent.

Par défaut, le `Button` rectangulaire, mais vous pouvez modifier les angles de l’informatique arrondi à l’aide de la [ `CornerRadius` ](xref:Xamarin.Forms.Button.CornerRadius) propriété, comme décrit ci-dessous dans la section [ **bouton apparence** ](#button-appearance).

Le [ `Text` ](xref:Xamarin.Forms.Button.Text) propriété spécifie le texte qui apparaît dans le `Button`. Le [ `Clicked` ](xref:Xamarin.Forms.Button.Clicked) événement est défini sur un gestionnaire d’événements nommé `OnButtonClicked`. Ce gestionnaire se trouve dans le fichier code-behind, **BasicButtonClickPage.xaml.cs**:

```csharp
public partial class BasicButtonClickPage : ContentPage
{
    public BasicButtonClickPage ()
    {
        InitializeComponent ();
    }

    async void OnButtonClicked(object sender, EventArgs args)
    {
        await label.RelRotateTo(360, 1000);
    }
}
```

Lorsque le `Button` drainé, le `OnButtonClicked` méthode s’exécute. Le `sender` argument est le `Button` objet responsable de cet événement. Vous pouvez l’utiliser pour accéder à la `Button` objet, ou pour faire la distinction entre plusieurs `Button` objets partagent le même `Clicked` événement.

Donnée `Clicked` gestionnaire appelle une fonction d’animation qui fait pivoter le `Label` 360 degrés de 1000 millisecondes. Voici le programme en cours d’exécution sur des appareils iOS et Android et comme une application de plateforme Windows universelle (UWP) sur le bureau Windows 10 :

[![Cliquez sur le bouton de base](button-images/BasicButtonClick.png "sur un bouton de base")](button-images/BasicButtonClick-Large.png#lightbox "sur un bouton de base")

Notez que la `OnButtonClicked` méthode inclut la `async` modificateur car `await` est utilisé dans le Gestionnaire d’événements. A `Clicked` Gestionnaire d’événements nécessite le `async` modificateur uniquement si le corps du Gestionnaire utilise `await`.

Chaque plate-forme restitue le `Button` dans sa propre manière spécifique. Dans le [ **bouton apparence** ](#button-appearance) section, vous allez apprendre à définir des couleurs et de rendre le `Button` bordure visible pour les affichages plus personnalisées. `Button` implémente le [ `IFontElement` ](xref:Xamarin.Forms.Internals.IFontElement) de l’interface, afin qu’il comporte [ `FontFamily` ](xref:Xamarin.Forms.Button.FontFamily), [ `FontSize` ](xref:Xamarin.Forms.Button.FontSize), et [ `FontAttributes` ](xref:Xamarin.Forms.Button.FontAttributes)propriétés.

## <a name="creating-a-button-in-code"></a>Création d’un bouton dans le code

Il est courant d’instancier un `Button` en XAML, mais vous pouvez également créer un `Button` dans le code. Cela peut s’avérer utile lorsque votre application doit créer plusieurs boutons basés sur des données énumérable avec un `foreach` boucle.

Le **Code Click du bouton** page montre comment créer une page qui est fonctionnellement équivalente à la **base Click du bouton** page mais entièrement en c# :

```csharp
public class CodeButtonClickPage : ContentPage
{
    public CodeButtonClickPage ()
    {
        Title = "Code Button Click";

        Label label = new Label
        {
            Text = "Click the Button below",
            FontSize = Device.GetNamedSize(NamedSize.Large, typeof(Label)),
            VerticalOptions = LayoutOptions.CenterAndExpand,
            HorizontalOptions = LayoutOptions.Center
        };

        Button button = new Button
        {
            Text = "Click to Rotate Text!",
            VerticalOptions = LayoutOptions.CenterAndExpand,
            HorizontalOptions = LayoutOptions.Center
        };
        button.Clicked += async (sender, args) => await label.RelRotateTo(360, 1000);

        Content = new StackLayout
        {
            Children =
            {
                label,
                button
            }
        };
    }
}
```

Tout est fait dans le constructeur de classe. Étant donné que le `Clicked` Gestionnaire n'est qu’une seule instruction longue, elle peut être jointe à l’événement très simple :

```csharp
button.Clicked += async (sender, args) => await label.RelRotateTo(360, 1000);
```

Bien entendu, vous pouvez également définir le Gestionnaire d’événements en tant qu’une méthode distincte (comme le `OnButtonClick` méthode dans **base Click du bouton**) et d’attachement de cette méthode à l’événement :

```csharp
button.Clicked += OnButtonClicked;
```

## <a name="disabling-the-button"></a>Désactivation du bouton

Parfois, une application est dans un état particulier où un particulier `Button` cliquez sur n’est pas une opération valide. Dans ce cas, le `Button` doit être désactivé en définissant son `IsEnabled` propriété `false`. L’exemple classique est une `Entry` contrôle pour un nom de fichier accompagné d’une ouverture de fichier `Button`: le `Button` doit être activée uniquement si du texte a été tapé dans le `Entry`.
Vous pouvez utiliser un `DataTrigger` pour cette tâche, comme indiqué dans le [ **déclencheurs Data** ](~/xamarin-forms/app-fundamentals/triggers.md#data-triggers) l’article.

## <a name="using-the-command-interface"></a>À l’aide de l’interface de commande

Il est possible pour une application répondre à `Button` clics sans gestion la `Clicked` événement. Le `Button` implémente un mécanisme de notification secondaire appelé le _commande_ ou _ordonnant_ interface. Il se compose de deux propriétés :

- [`Command`](xref:Xamarin.Forms.Button.Command) de type [ `ICommand` ](xref:System.Windows.Input.ICommand), une interface définie dans le [ `System.Windows.Input` ](xref:System.Windows.Input) espace de noms.
- [`CommandParameter`](xref:Xamarin.Forms.Button.CommandParameter) propriété de type [ `Object` ](xref:System.Object).

Cette approche est particulièrement appropriée en fonction de la liaison de données et en particulier lors de l’implémentation de l’architecture Model-View-ViewModel (MVVM). Ces rubriques sont présentées dans les articles [une liaison de données](~/xamarin-forms/app-fundamentals/data-binding/index.md), [à partir des liaisons de données à MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md), et [MVVM](~/xamarin-forms/enterprise-application-patterns/mvvm.md).

Dans une application MVVM, ce dernier définit les propriétés de type `ICommand` qui sont alors connectés au XAML `Button` éléments avec des liaisons de données. Xamarin.Forms définit également [ `Command` ]((xref:Xamarin.Forms.Command`1)) et [ `Command<T>` ](xref:Xamarin.Forms.Command`1) classes qui implémentent le `ICommand` de l’interface et faciliter le ViewModel dans la définition des propriétés de type `ICommand`.

Exécution des commandes sont décrite en détail dans l’article [ **l’Interface de commande** ](~/xamarin-forms/app-fundamentals/data-binding/commanding.md) mais la **base bouton de commande** page dans le [  **ButtonDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ButtonDemos) montre l’approche de base.

Le `CommandDemoViewModel` classe est un ViewModel très simple qui définit une propriété de type `double` nommé `Number`et deux propriétés de type `ICommand` nommé `MultiplyBy2Command` et `DivideBy2Command`:

```csharp
class CommandDemoViewModel : INotifyPropertyChanged
{
    double number = 1;

    public event PropertyChangedEventHandler PropertyChanged;

    public CommandDemoViewModel()
    {
        MultiplyBy2Command = new Command(() => Number *= 2);

        DivideBy2Command = new Command(() => Number /= 2);
    }

    public double Number
    {
        set
        {
            if (number != value)
            {
                number = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Number"));
            }
        }
        get
        {
            return number;
        }
    }

    public ICommand MultiplyBy2Command { private set; get; }

    public ICommand DivideBy2Command { private set; get; }
}
```

Les deux `ICommand` propriétés sont initialisées dans le constructeur de classe avec les deux objets de type `Command`. Le `Command` constructeurs incluent une petite fonction (appelée la `execute` argument du constructeur) qui double ou divise par deux le `Number` propriété.

Le **BasicButtonCommand.xaml** fichier définit son `BindingContext` à une instance de `CommandDemoViewModel`. Le `Label` élément et deux `Button` éléments contiennent des liaisons dans les trois propriétés `CommandDemoViewModel`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:ButtonDemos"
             x:Class="ButtonDemos.BasicButtonCommandPage"
             Title="Basic Button Command">

    <ContentPage.BindingContext>
        <local:CommandDemoViewModel />
    </ContentPage.BindingContext>

    <StackLayout>
        <Label Text="{Binding Number, StringFormat='Value is now {0}'}"
               FontSize="Large"
               VerticalOptions="CenterAndExpand"
               HorizontalOptions="Center" />

        <Button Text="Multiply by 2"
                VerticalOptions="CenterAndExpand"
                HorizontalOptions="Center"
                Command="{Binding MultiplyBy2Command}" />

        <Button Text="Divide by 2"
                VerticalOptions="CenterAndExpand"
                HorizontalOptions="Center"
                Command="{Binding DivideBy2Command}" />
    </StackLayout>
</ContentPage>
```

Comme les deux `Button` éléments sont tapées, les commandes sont exécutées, et le nombre change de valeur :

[![Commande de base du bouton](button-images/BasicButtonCommand.png "base bouton de commande")](button-images/BasicButtonCommand-Large.png#lightbox)

L’avantage de cette approche sur `Clicked` gestionnaires est que toute la logique qui impliquent la fonctionnalité de cette page se trouve dans le ViewModel plutôt que dans le fichier code-behind, pour parvenir à une meilleure séparation de l’interface utilisateur de la logique métier.

Il est également possible pour le `Command` objets pour contrôler l’activation et la désactivation de la `Button` éléments. Par exemple, supposons que vous souhaitez limiter la plage des valeurs numériques entre 2<sup>10</sup> et 2<sup>&ndash;10</sup>. Vous pouvez ajouter une autre fonction au constructeur (appelée la `canExecute` argument) qui retourne `true` si le `Button` doit être activée. Voici la modification de la `CommandDemoViewModel` constructeur :

```csharp
class CommandDemoViewModel : INotifyPropertyChanged
{
    ···
    public CommandDemoViewModel()
    {
        MultiplyBy2Command = new Command(
            execute: () =>
            {
                Number *= 2;
                ((Command)MultiplyBy2Command).ChangeCanExecute();
                ((Command)DivideBy2Command).ChangeCanExecute();
            },
            canExecute: () => Number < Math.Pow(2, 10));

        DivideBy2Command = new Command(
            execute: () =>
            {
                Number /= 2;
                ((Command)MultiplyBy2Command).ChangeCanExecute();
                ((Command)DivideBy2Command).ChangeCanExecute();
            },
            canExecute: () => Number > Math.Pow(2, -10));
    }
    ···
}
```

Les appels à la `ChangeCanExecute` méthode de `Command` sont nécessaires afin que les `Command` méthode peut appeler le `canExecute` (méthode) et déterminer si le `Button` doit être désactivée ou non. Avec cette modification du code, comme le nombre atteint la limite, le `Button` est désactivé :

[![Commande de bouton de base - modifiée](button-images/BasicButtonCommandModified.png "commande de bouton de base - modifiée")](button-images/BasicButtonCommandModified-Large.png#lightbox)

Il est possible que deux ou plusieurs `Button` éléments pour être lié à la même `ICommand` propriété. Le `Button` peuvent être distingués des éléments à l’aide de la [ `CommandParameter` ](xref:Xamarin.Forms.Button.CommandParameter) propriété du `Button`. Dans ce cas, que vous souhaitez utiliser le type générique [ `Command<T>` ](xref:Xamarin.Forms.Command`1) classe. Le `CommandParameter` objet est ensuite passé comme argument à la `execute` et `canExecute` méthodes. Cette technique est décrite en détail dans les [ **ordonnant base** ](~/xamarin-forms/app-fundamentals/data-binding/commanding.md#basic-commanding) section de la [ **Interface de commande** ](~/xamarin-forms/app-fundamentals/data-binding/commanding.md#basic-commanding) l’article.

Le [ **ButtonDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ButtonDemos) utilise également cette technique dans son `MainPage` classe. Le **MainPage.xaml** fichier contient un `Button` pour chaque page de l’exemple :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:ButtonDemos"
             x:Class="ButtonDemos.MainPage"
             Title="Button Demos">
    <ScrollView>
        <FlexLayout Direction="Column"
                    JustifyContent="SpaceEvenly"
                    AlignItems="Center">

            <Button Text="Basic Button Click"
                    Command="{Binding NavigateCommand}"
                    CommandParameter="{x:Type local:BasicButtonClickPage}" />

            <Button Text="Code Button Click"
                    Command="{Binding NavigateCommand}"
                    CommandParameter="{x:Type local:CodeButtonClickPage}" />

            <Button Text="Basic Button Command"
                    Command="{Binding NavigateCommand}"
                    CommandParameter="{x:Type local:BasicButtonCommandPage}" />

            <Button Text="Press and Release Button"
                    Command="{Binding NavigateCommand}"
                    CommandParameter="{x:Type local:PressAndReleaseButtonPage}" />

            <Button Text="Button Appearance"
                    Command="{Binding NavigateCommand}"
                    CommandParameter="{x:Type local:ButtonAppearancePage}" />

            <Button Text="Toggle Button Demo"
                    Command="{Binding NavigateCommand}"
                    CommandParameter="{x:Type local:ToggleButtonDemoPage}" />

            <Button Text="Image Button Demo"
                    Command="{Binding NavigateCommand}"
                    CommandParameter="{x:Type local:ImageButtonDemoPage}" />

        </FlexLayout>
    </ScrollView>
</ContentPage>
```

Chaque `Button` a son `Command` propriété liée à une propriété nommée `NavigateCommand`et le `CommandParameter` est définie sur une [ `Type` ](xref:System.Type) objet correspondant à une des classes de page dans le projet.

Que `NavigateCommand` propriété est de type `ICommand` et est défini dans le fichier code-behind :

```csharp
public partial class MainPage : ContentPage
{
    public MainPage()
    {
        InitializeComponent();

        NavigateCommand = new Command<Type>(async (Type pageType) =>
        {
            Page page = (Page)Activator.CreateInstance(pageType);
            await Navigation.PushAsync(page);
        });

        BindingContext = this;
    }

    public ICommand NavigateCommand { private set; get; }
}
```

Le constructeur initialise la `NavigateCommand` propriété un `Command<Type>` , car l’objet `Type` est le type de la `CommandParameter` jeu d’objets dans le fichier XAML. Cela signifie que la `execute` méthode possède un argument de type `Type` qui correspond à ce `CommandParameter` objet. La fonction instancie la page et accède ensuite à ce dernier.

Notez que le constructeur se termine en définissant son `BindingContext` à lui-même. Cela est nécessaire pour les propriétés dans le fichier XAML à lier à la `NavigateCommand` propriété.

## <a name="pressing-and-releasing-the-button"></a>En appuyant sur et relâcher le bouton.

Outre la `Clicked` événement, `Button` définit également [ `Pressed` ](xref:Xamarin.Forms.Button.Pressed) et [ `Released` ](xref:Xamarin.Forms.Button.Released) événements. Le `Pressed` événement se produit lorsqu’un doigt appuie sur une `Button`, ou un bouton de la souris est enfoncé avec le pointeur sur le `Button`. Le `Released` événement se produit lorsque le bouton doigt ou de la souris est relâché. En règle générale, un `Clicked` événement est également déclenché en même temps que la `Released` événement, mais si le pointeur de souris ou à un doigt diapositives en s’éloignant de la surface de la `Button` avant d’être publiées, la `Clicked` événement ne peut pas se produire.

Le `Pressed` et `Released` événements ne sont pas souvent utilisés, mais ils peuvent être utilisés à des fins spéciales, comme illustré dans le **bouton de mise en production** page. Le fichier XAML contient un `Label` et un `Button` avec les gestionnaires joints pour le `Pressed` et `Released` événements :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ButtonDemos.PressAndReleaseButtonPage"
             Title="Press and Release Button">
    <StackLayout>

        <Label x:Name="label"
               Text="Press and hold the Button below"
               FontSize="Large"
               VerticalOptions="CenterAndExpand"
               HorizontalOptions="Center" />

        <Button Text="Press to Rotate Text!"
                VerticalOptions="CenterAndExpand"
                HorizontalOptions="Center"
                Pressed="OnButtonPressed"
                Released="OnButtonReleased" />

    </StackLayout>
</ContentPage>
```

Le fichier code-behind anime le `Label` lorsqu’un `Pressed` l’événement se produit, mais interrompt la rotation lorsqu’un `Released` événement se produit :

```csharp
public partial class PressAndReleaseButtonPage : ContentPage
{
    bool animationInProgress = false;
    Stopwatch stopwatch = new Stopwatch();

    public PressAndReleaseButtonPage ()
    {
        InitializeComponent ();
    }

    void OnButtonPressed(object sender, EventArgs args)
    {
        stopwatch.Start();
        animationInProgress = true;

        Device.StartTimer(TimeSpan.FromMilliseconds(16), () =>
        {
            label.Rotation = 360 * (stopwatch.Elapsed.TotalSeconds % 1);

            return animationInProgress;
        });
    }

    void OnButtonReleased(object sender, EventArgs args)
    {
        animationInProgress = false;
        stopwatch.Stop();
    }
}
```

Le résultat est que le `Label` uniquement utilise un doigt en contact avec la `Button`et s’arrête lorsque le doigt est publié :

[![Appuyez sur et relâchez le bouton](button-images/PressAndReleaseButton.png "appuyez et relâchez le bouton")](button-images/PressAndReleaseButton-Large.png)

Ce type de comportement a des applications pour les jeux : un doigt maintenu sur une `Button` risque de rendre un objet d’écran à déplacer dans une direction particulière.

<a name="button-appearance" />

## <a name="button-appearance"></a>Apparence du bouton

Le `Button` hérite ou définit plusieurs propriétés qui affectent son apparence :

- [`TextColor`](xref:Xamarin.Forms.Button.TextColor) est la couleur de la `Button` texte
- [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) est la couleur d’arrière-plan pour que le texte
- [`BorderColor`](xref:Xamarin.Forms.Button.BorderColor) est la couleur d’une zone entourant le `Button`
- [`FontFamily`](xref:Xamarin.Forms.Button.FontFamily) la famille de polices est utilisée pour le texte
- [`FontSize`](xref:Xamarin.Forms.Button.FontSize) la taille du texte
- [`FontAttributes`](xref:Xamarin.Forms.Button.FontAttributes) Indique si le texte est en italique ou en gras
- [`BorderWidth`](xref:Xamarin.Forms.Button.BorderWidth) la largeur de la bordure
- [`CornerRadius`](xref:Xamarin.Forms.Button.CornerRadius) Arrondit les angles

Les effets de six de ces propriétés (à l’exclusion des `FontFamily` et `FontAttributes`) sont illustrées dans le **apparence du bouton** page. Une autre propriété, [ `Image` ](xref:Xamarin.Forms.Button.Image), est décrite dans la section [ **à l’aide de bitmaps avec le bouton**](#image-button).

Toutes les liaisons de données et les vues dans le **apparence du bouton** page sont définies dans le fichier XAML :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:ButtonDemos"
             x:Class="ButtonDemos.ButtonAppearancePage"
             Title="Button Appearance">
    <StackLayout>
        <Button x:Name="button"
                Text="Button"
                VerticalOptions="CenterAndExpand"
                HorizontalOptions="Center"
                TextColor="{Binding Source={x:Reference textColorPicker},
                                    Path=SelectedItem.Color}"
                BackgroundColor="{Binding Source={x:Reference backgroundColorPicker},
                                          Path=SelectedItem.Color}"
                BorderColor="{Binding Source={x:Reference borderColorPicker},
                                      Path=SelectedItem.Color}" />

        <StackLayout BindingContext="{x:Reference button}"
                     Padding="10">

            <Slider x:Name="fontSizeSlider"
                    Maximum="48"
                    Minimum="1"
                    Value="{Binding FontSize}" />

            <Label Text="{Binding Source={x:Reference fontSizeSlider},
                                  Path=Value,
                                  StringFormat='FontSize = {0:F0}'}"
                   HorizontalTextAlignment="Center" />

            <Slider x:Name="borderWidthSlider"
                    Minimum="-1"
                    Maximum="12"
                    Value="{Binding BorderWidth}" />

            <Label Text="{Binding Source={x:Reference borderWidthSlider},
                                  Path=Value,
                                  StringFormat='BorderWidth = {0:F0}'}"
                   HorizontalTextAlignment="Center" />

            <Slider x:Name="cornerRadiusSlider"
                    Minimum="-1"
                    Maximum="24"
                    Value="{Binding CornerRadius}" />

            <Label Text="{Binding Source={x:Reference cornerRadiusSlider},
                                  Path=Value,
                                  StringFormat='CornerRadius = {0:F0}'}"
                   HorizontalTextAlignment="Center" />

            <Grid>
                <Grid.RowDefinitions>
                    <RowDefinition Height="Auto" />
                    <RowDefinition Height="Auto" />
                    <RowDefinition Height="Auto" />
                </Grid.RowDefinitions>

                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>

                <Grid.Resources>
                    <Style TargetType="Label">
                        <Setter Property="VerticalOptions" Value="Center" />
                    </Style>
                </Grid.Resources>

                <Label Text="Text Color:"
                       Grid.Row="0" Grid.Column="0" />

                <Picker x:Name="textColorPicker"
                        ItemsSource="{Binding Source={x:Static local:NamedColor.All}}"
                        ItemDisplayBinding="{Binding FriendlyName}"
                        SelectedIndex="0"
                        Grid.Row="0" Grid.Column="1" />

                <Label Text="Background Color:"
                       Grid.Row="1" Grid.Column="0" />

                <Picker x:Name="backgroundColorPicker"
                        ItemsSource="{Binding Source={x:Static local:NamedColor.All}}"
                        ItemDisplayBinding="{Binding FriendlyName}"
                        SelectedIndex="0"
                        Grid.Row="1" Grid.Column="1" />

                <Label Text="Border Color:"
                       Grid.Row="2" Grid.Column="0" />

                <Picker x:Name="borderColorPicker"
                        ItemsSource="{Binding Source={x:Static local:NamedColor.All}}"
                        ItemDisplayBinding="{Binding FriendlyName}"
                        SelectedIndex="0"
                        Grid.Row="2" Grid.Column="1" />
            </Grid>
        </StackLayout>
    </StackLayout>
</ContentPage>
```

Le `Button` en haut de la page a ses trois `Color` propriétés liées aux `Picker` éléments en bas de la page. Les éléments de la `Picker` les éléments sont des couleurs à partir de la `NamedColor` classe inclus dans le projet. Trois `Slider` les éléments contiennent des liaisons bidirectionnelles pour le `FontSize`, `BorderWidth`, et `CornerRadius` propriétés de la `Button`.

Ce programme vous permet de faire des essais avec les combinaisons de toutes ces propriétés :

[![Bouton apparence](button-images/ButtonAppearance.png "bouton apparence")](button-images/ButtonAppearance-Large.png)

Pour voir les `Button` bordure, vous devez définir un `BorderColor` une valeur autre que `Default`et le `BorderWidth` à une valeur positive.

Sur iOS, vous remarquerez que les largeurs de grande taille de bordure empiéter à l’intérieur de la `Button` et interférer avec l’affichage du texte. Si vous choisissez d’utiliser une bordure avec un iOS `Button`, vous souhaiterez probablement commencer et se terminer le `Text` propriété avec des espaces pour conserver sa visibilité.

Sur la plateforme Windows universelle, en sélectionnant un `CornerRadius` qui dépasse la moitié de la hauteur de la `Button` lève une exception.

## <a name="creating-a-toggle-button"></a>Création d’un bouton bascule

Il est possible de sous-classe `Button` pour qu’il fonctionne comme un commutateur marche / arrêt : appuyez sur le bouton une fois pour le bouton bascule sur et cliquez sur Nouveau pour la désactiver.

Les éléments suivants `ToggleButton` dérive de la classe `Button` et définit un événement nommé `Toggled` et une propriété booléenne nommée `IsToggled`. Voici les deux mêmes propriétés définies par le Xamarin.Forms [ `Switch` ](xref:Xamarin.Forms.Switch):

```csharp
class ToggleButton : Button
{
    public event EventHandler<ToggledEventArgs> Toggled;

    public static BindableProperty IsToggledProperty =
        BindableProperty.Create("IsToggled", typeof(bool), typeof(ToggleButton), false,
                                propertyChanged: OnIsToggledChanged);

    public ToggleButton()
    {
        Clicked += (sender, args) => IsToggled ^= true;
    }

    public bool IsToggled
    {
        set { SetValue(IsToggledProperty, value); }
        get { return (bool)GetValue(IsToggledProperty); }
    }

    protected override void OnParentSet()
    {
        base.OnParentSet();
        VisualStateManager.GoToState(this, "ToggledOff");
    }

    static void OnIsToggledChanged(BindableObject bindable, object oldValue, object newValue)
    {
        ToggleButton toggleButton = (ToggleButton)bindable;
        bool isToggled = (bool)newValue;

        // Fire event
        toggleButton.Toggled?.Invoke(toggleButton, new ToggledEventArgs(isToggled));

        // Set the visual state
        VisualStateManager.GoToState(toggleButton, isToggled ? "ToggledOn" : "ToggledOff");
    }
}
```

Le `ToggleButton` constructeur attache un gestionnaire à la `Clicked` événement afin qu’elle peut modifier la valeur de la `IsToggled` propriété. Le `OnIsToggledChanged` se déclenche la méthode le `Toggled` événement.

La dernière ligne de la `OnIsToggledChanged` méthode appelle la méthode statique `VisualStateManager.GoToState` méthode avec le texte de deux chaînes « ToggledOn » et « ToggledOff ». Vous pouvez lire sur cette méthode et la façon dont votre application peut répondre à des états visuels dans l’article [ **le Gestionnaire d’état visuel Xamarin.Forms**](~/xamarin-forms/user-interface/visual-state-manager.md).

Étant donné que `ToggleButton` effectue l’appel à `VisualStateManager.GoToState`, la classe elle-même n’a pas besoin d’inclure des fonctionnalités supplémentaires pour modifier l’apparence du bouton en fonction de son `IsToggled` état. Qui est la responsabilité du code XAML qui héberge le `ToggleButton`.

Le **démonstration de bouton bascule** page contient deux instances de `ToggleButton`, y compris le balisage de gestionnaire d’état visuel qui définit les `Text`, `BackgroundColor`, et `TextColor` du bouton en fonction de l’état visuel :

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:ButtonDemos"
             x:Class="ButtonDemos.ToggleButtonDemoPage"
             Title="Toggle Button Demo">

    <ContentPage.Resources>
        <Style TargetType="local:ToggleButton">
            <Setter Property="VerticalOptions" Value="CenterAndExpand" />
            <Setter Property="HorizontalOptions" Value="Center" />
        </Style>
    </ContentPage.Resources>

    <StackLayout Padding="10, 0">
        <local:ToggleButton Toggled="OnItalicButtonToggled">
            <VisualStateManager.VisualStateGroups>
                <VisualStateGroup Name="ToggleStates">
                    <VisualState Name="ToggledOff">
                        <VisualState.Setters>
                            <Setter Property="Text" Value="Italic Off" />
                            <Setter Property="BackgroundColor" Value="#C0C0C0" />
                            <Setter Property="TextColor" Value="Black" />
                        </VisualState.Setters>
                    </VisualState>

                    <VisualState Name="ToggledOn">
                        <VisualState.Setters>
                            <Setter Property="Text" Value=" Italic On " />
                            <Setter Property="BackgroundColor" Value="#404040" />
                            <Setter Property="TextColor" Value="White" />
                        </VisualState.Setters>
                    </VisualState>
                </VisualStateGroup>
            </VisualStateManager.VisualStateGroups>
        </local:ToggleButton>

        <local:ToggleButton Toggled="OnBoldButtonToggled">
            <VisualStateManager.VisualStateGroups>
                <VisualStateGroup Name="ToggleStates">
                    <VisualState Name="ToggledOff">
                        <VisualState.Setters>
                            <Setter Property="Text" Value="Bold Off" />
                            <Setter Property="BackgroundColor" Value="#C0C0C0" />
                            <Setter Property="TextColor" Value="Black" />
                        </VisualState.Setters>
                    </VisualState>

                    <VisualState Name="ToggledOn">
                        <VisualState.Setters>
                            <Setter Property="Text" Value=" Bold On " />
                            <Setter Property="BackgroundColor" Value="#404040" />
                            <Setter Property="TextColor" Value="White" />
                        </VisualState.Setters>
                    </VisualState>
                </VisualStateGroup>
            </VisualStateManager.VisualStateGroups>
        </local:ToggleButton>

        <Label x:Name="label"
               Text="Just a little passage of some sample text that can be formatted in italic or boldface by toggling the two buttons."
               FontSize="Large"
               HorizontalTextAlignment="Center"
               VerticalOptions="CenterAndExpand" />

    </StackLayout>
</ContentPage>
```

Le `Toggled` sont des gestionnaires d’événements dans le fichier code-behind. Dont il est responsable de paramètre le `FontAttributes` propriété de la `Label` basée sur l’état des boutons :

```csharp
public partial class ToggleButtonDemoPage : ContentPage
{
    public ToggleButtonDemoPage ()
    {
        InitializeComponent ();
    }

    void OnItalicButtonToggled(object sender, ToggledEventArgs args)
    {
        if (args.Value)
        {
            label.FontAttributes |= FontAttributes.Italic;
        }
        else
        {
            label.FontAttributes &= ~FontAttributes.Italic;
        }
    }

    void OnBoldButtonToggled(object sender, ToggledEventArgs args)
    {
        if (args.Value)
        {
            label.FontAttributes |= FontAttributes.Bold;
        }
        else
        {
            label.FontAttributes &= ~FontAttributes.Bold;
        }
    }
}
```

Voici le programme en cours d’exécution sur iOS, Android et la plateforme Windows universelle :

[![Activer/désactiver le bouton démonstration](button-images/ToggleButtonDemo.png "activer/désactiver la démonstration de bouton")](button-images/ToggleButtonDemo-Large.png#lightbox)

<a name="image-button" />

## <a name="using-bitmaps-with-buttons"></a>À l’aide de bitmaps des boutons

Le `Button` classe définit un [ `Image` ](xref:Xamarin.Forms.Button.Image) propriété qui vous permet d’afficher une image bitmap sur le `Button`, seul ou en combinaison avec du texte. Vous pouvez également spécifier la façon dont le texte et image sont réorganisés.

Le `Image` propriété est de type [ `FileImageSource` ](xref:Xamarin.Forms.FileImageSource), ce qui signifie que les bitmaps doivent être stockés en tant que ressources dans les projets de plateforme individuels et non dans le projet de bibliothèque .NET Standard.

Chaque plateforme prise en charge par Xamarin.Forms permet d’être stockées dans plusieurs tailles pour les résolutions de pixels différente des périphériques différents de l’application peut s’exécuter sur les images. Ces bitmaps plusieurs sont nommés ou stockés de manière à ce que le système d’exploitation peut choisir la meilleure correspondance pour le périphérique vidéo résolution d’affichage.

Pour une image bitmap sur un `Button`, la taille optimale est généralement entre 32 et 64 unités d’indépendant du périphérique, selon la taille que vous souhaitez qu’il le soit. Les images utilisées dans cet exemple sont basées sur une taille de 48 unités indépendantes du périphérique.

Dans le projet iOS, le **ressources** dossier contient trois tailles de cette image :

- Une image bitmap carrée 48 pixels stockée en tant que **/Resources/MonkeyFace.png**
- Une image bitmap carrée 96 pixels stockée en tant que **/Resource/MonkeyFace@2x.png**
- Une image bitmap carrée 144 pixels stockée en tant que **/Resource/MonkeyFace@3x.png**

Tous les trois bitmaps ont eu une **Action de génération** de **BundleResource**.

Pour le projet Android, les bitmaps toutes avoir le même nom, mais ils sont stockés dans des sous-dossiers différents de la **ressources** dossier :

- Une image bitmap carrée 72 pixels stockée en tant que **/Resources/drawable-hdpi/MonkeyFace.png**
- Une image bitmap carrée 96 pixels stockée en tant que **/Resources/drawable-xhdpi/MonkeyFace.png**
- Une image bitmap carrée 144 pixels stockée en tant que **/Resources/drawable-xxhdpi/MonkeyFace.png**
- Une image bitmap carrée 192 pixels stockée en tant que **/Resources/drawable-xxxhdpi/MonkeyFace.png**

Ils ont été donnés un **Action de génération** de **AndroidResource**.

Dans le projet UWP, bitmaps peut figurer n’importe où dans le projet, mais ils sont généralement stockés dans un dossier personnalisé ou le **actifs** dossier existant. Le projet UWP contient ces images bitmap :

- Une image bitmap carrée 48 pixels stockée en tant que **/Assets/MonkeyFace.scale-100.png**
- Une image bitmap carrée 96 pixels stockée en tant que **/Assets/MonkeyFace.scale-200.png**
- Une image bitmap carrée 192 pixels stockée en tant que **/Assets/MonkeyFace.scale-400.png**

Ils ont eu une **Action de génération** de **contenu**.

Vous pouvez spécifier comment les `Text` et `Image` propriétés sont organisées sur le `Button` à l’aide de la [ `ContentLayout` ](xref:Xamarin.Forms.Button.ContentLayout) propriété de `Button`. Cette propriété est de type [ `ButtonContentLayout` ](xref:Xamarin.Forms.Button.ButtonContentLayout), qui est une classe incorporée dans `Button`. Le [constructeur](xref:Xamarin.Forms.Button.ButtonContentLayout.%23ctor(Xamarin.Forms.Button.ButtonContentLayout.ImagePosition,System.Double)) a deux arguments :

- Un membre de la [ `ImagePosition` ](xref:Xamarin.Forms.Button.ButtonContentLayout.ImagePosition) énumération : `Left`, `Top`, `Right`, ou `Bottom` indiquant comment l’image bitmap s’affiche par rapport au texte.
- A `double` valeur pour l’espacement entre l’image bitmap et le texte.

Les valeurs par défaut sont `Left` et 10 unités. Deux propriétés en lecture seule de `ButtonContentLayout` nommé [ `Position` ](xref:Xamarin.Forms.Button.ButtonContentLayout.Position) et [ `Spacing` ](xref:Xamarin.Forms.Button.ButtonContentLayout.Spacing) fournissent les valeurs de ces propriétés.

Dans le code, vous pouvez créer un `Button` et définir le `ContentLayout` propriété comme suit :

```csharp
Button button = new Button
{
    Text = "button text",
    Image = new FileImageSource
    {
        File = "image filename"
    },
    ContentLayout = new Button.ButtonContentLayout(Button.ButtonContentLayout.ImagePosition.Right, 20)
};
```

En XAML, vous devez spécifier uniquement le membre d’énumération, ou l’espacement, ou à la fois dans n’importe quel ordre séparant par des virgules :

```xaml
<Button Text="button text"
        Image="image filename"
        ContentLayout="Right, 20" />
```

Le **démonstration de bouton d’Image** page utilise `OnPlatform` pour spécifier les noms de fichier différents pour iOS, Android, les UWP des fichiers bitmap. Si vous souhaitez utiliser le même nom de fichier pour les trois plateformes et évitez d’utiliser `OnPlatform`, vous aurez besoin stocker les images de plateforme Windows universelle dans le répertoire racine du projet.

La première `Button` sur la **démonstration de bouton d’Image** page définit le `Image` propriété mais pas le `Text` propriété :

```xaml
<Button>
    <Button.Image>
        <OnPlatform x:TypeArguments="FileImageSource">
            <On Platform="iOS, Android" Value="MonkeyFace.png" />
            <On Platform="UWP" Value="Assets/MonkeyFace.png" />
        </OnPlatform>
    </Button.Image>
</Button>
```

Si les bitmaps UWP sont stockés dans le répertoire racine du projet, ce balisage peut être considérablement simplifié :

```xaml
<Button Image="MonkeyFace.png" />
```

Pour éviter un grand nombre de répétitions balisage dans le **ImageButtonDemo.xaml** fichier implicite `Style` est également défini pour définir le `Image` propriété. Cela `Style` est appliquée automatiquement à cinq autres `Button` éléments. Voici le fichier XAML complet :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ButtonDemos.ImageButtonDemoPage">

    <FlexLayout Direction="Column"
                JustifyContent="SpaceEvenly"
                AlignItems="Center">

        <FlexLayout.Resources>
            <Style TargetType="Button">
                <Setter Property="Image">
                    <OnPlatform x:TypeArguments="FileImageSource">
                        <On Platform="iOS, Android" Value="MonkeyFace.png" />
                        <On Platform="UWP" Value="Assets/MonkeyFace.png" />
                    </OnPlatform>
                </Setter>
            </Style>
        </FlexLayout.Resources>

        <Button>
            <Button.Image>
                <OnPlatform x:TypeArguments="FileImageSource">
                    <On Platform="iOS, Android" Value="MonkeyFace.png" />
                    <On Platform="UWP" Value="Assets/MonkeyFace.png" />
                </OnPlatform>
            </Button.Image>
        </Button>

        <Button Text="Default" />

        <Button Text="Left - 10"
                ContentLayout="Left, 10" />

        <Button Text="Top - 10"
                ContentLayout="Top, 10" />

        <Button Text="Right - 20"
                ContentLayout="Right, 20" />

        <Button Text="Bottom - 20"
                ContentLayout="Bottom, 20" />
    </FlexLayout>
</ContentPage>
```

Les quatre finals `Button` les éléments utilisent le `ContentLayout` propriété pour spécifier une position et l’espacement du texte et image bitmap :

[![Démonstration de bouton d’image](button-images/ImageButtonDemo.png "démonstration de bouton d’Image")](button-images/ImageButtonDemo-Large.png#lightbox)

Vous avez vu maintenant les différentes méthodes que vous pouvez gérer `Button` événements et modifier le `Button` apparence.

## <a name="related-links"></a>Liens connexes

- [Exemple de ButtonDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ButtonDemos)
- [Bouton API](xref:Xamarin.Forms.Button)
