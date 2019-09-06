---
title: Bouton de Xamarin.Forms
description: Le bouton répond à un cliquez ou appuyez sur dirigeant d’une application à exécuter une tâche particulière.
ms.prod: xamarin
ms.assetid: 62CAEB63-0800-44F4-9B8C-EE632138C2F5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/19/2018
ms.openlocfilehash: cdf89b55c30b0a4e7ab247c396a870e0bad24886
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70287712"
---
# <a name="xamarinforms-button"></a>Bouton de Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos)

_Le bouton répond à un cliquez ou appuyez sur dirigeant d’une application à exécuter une tâche particulière._

Le [ `Button` ](xref:Xamarin.Forms.Button) est le plus fondamental contrôle interactif dans tous les de Xamarin.Forms. Le `Button` généralement affiche une courte chaîne de texte qui indique une commande, mais il peut également affiche une image bitmap, ou une combinaison de texte et une image. L’utilisateur appuie sur le `Button` avec un doigt ou clique dessus avec la souris pour exécuter cette commande.

La plupart des sujets présentés ci-dessous correspondre aux pages dans le [ **ButtonDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos) exemple.

## <a name="handling-button-clicks"></a>Gestion bouton clique sur

`Button` définit un [ `Clicked` ](xref:Xamarin.Forms.Button.Clicked) événement est déclenché lorsque l’utilisateur actionne le `Button` avec un pointeur doigt ou de la souris. L’événement est déclenché lorsque le bouton doigt ou de la souris est relâché à partir de la surface de la `Button`. Le `Button` doit avoir son [ `IsEnabled` ](xref:Xamarin.Forms.VisualElement.IsEnabled) propriété définie sur `true` pour pouvoir répondre aux clics.

Le **base clic de bouton** page dans le [ **ButtonDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos) exemple montre comment instancier un `Button` dans XAML et de gérer ses `Clicked` événement. Le **BasicButtonClickPage.xaml** fichier contient un `StackLayout` avec à la fois un `Label` et un `Button`:

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

Le `Button` a tendance à occuper tout l’espace qui est autorisé pour celui-ci. Par exemple, si vous ne définissez pas la `HorizontalOptions` propriété du `Button` une valeur autre que `Fill`, le `Button` occupe toute la largeur de son parent.

Par défaut, le `Button` est rectangulaire, mais vous pouvez donner des angles d’informatique arrondi à l’aide de la [ `CornerRadius` ](xref:Xamarin.Forms.Button.CornerRadius) propriété, comme décrit ci-dessous dans la section [ **bouton apparence** ](#button-appearance).

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

Lorsque le `Button` est activé par un clic, le `OnButtonClicked` méthode s’exécute. Le `sender` argument est le `Button` objet responsable de cet événement. Vous pouvez l’utiliser pour accéder à la `Button` objet, ou faire la distinction entre plusieurs `Button` objets partagent la même `Clicked` événement.

Cette particulier `Clicked` gestionnaire appelle une fonction d’animation qui fait pivoter le `Label` 360 degrés de 1000 millisecondes. Voici le programme en cours d’exécution sur des appareils iOS et Android et comme une application de plateforme universelle Windows (UWP) sur le bureau Windows 10 :

[![Cliquez sur le bouton de base](button-images/BasicButtonClick.png "base clic")](button-images/BasicButtonClick-Large.png#lightbox "cliquez sur le bouton de base")

Notez que le `OnButtonClicked` méthode inclut la `async` modificateur car `await` est utilisé dans le Gestionnaire d’événements. Un `Clicked` Gestionnaire d’événements nécessite le `async` modificateur uniquement si le corps du Gestionnaire utilise `await`.

Chaque plateforme restitue le `Button` dans sa propre manière spécifique. Dans le [ **bouton apparence** ](#button-appearance) section, vous allez apprendre à définir des couleurs et de rendre le `Button` bordure est visible pour les affichages plus personnalisées. `Button` implémente le [ `IFontElement` ](xref:Xamarin.Forms.Internals.IFontElement) interface, et inclut donc [ `FontFamily` ](xref:Xamarin.Forms.Button.FontFamily), [ `FontSize` ](xref:Xamarin.Forms.Button.FontSize), et [ `FontAttributes` ](xref:Xamarin.Forms.Button.FontAttributes)propriétés.

## <a name="creating-a-button-in-code"></a>Création d’un bouton dans le code

Il est courant d’instancier un `Button` dans XAML, mais vous pouvez également créer un `Button` dans le code. Cela peut être pratique lorsque votre application a besoin créer plusieurs boutons basés sur des données qui sont énumérables avec un `foreach` boucle.

Le **clic de bouton Code** page montre comment créer une page qui est fonctionnellement équivalente à la **clic de bouton base** page mais entièrement en C#:

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

Tout est fait dans le constructeur de la classe. Étant donné que le `Clicked` Gestionnaire n'est qu’une seule instruction longue, elle peut être jointe à l’événement très simples :

```csharp
button.Clicked += async (sender, args) => await label.RelRotateTo(360, 1000);
```

Bien sûr, vous pouvez également définir le Gestionnaire d’événements comme une méthode distincte (tout comme le `OnButtonClick` méthode dans **clic de bouton base**) et attacher cette méthode à l’événement :

```csharp
button.Clicked += OnButtonClicked;
```

## <a name="disabling-the-button"></a>Désactivation du bouton

Parfois, une application est dans un état particulier où un particulier `Button` clic n’est pas une opération valide. Dans ce cas, le `Button` doit être désactivée en définissant son `IsEnabled` propriété `false`. L’exemple classique est un `Entry` contrôle pour un nom de fichier accompagné d’un fichier `Button`-Open : L' `Button` option doit être activée uniquement si du texte a été tapé dans `Entry`le.
Vous pouvez utiliser un `DataTrigger` pour cette tâche, comme indiqué dans le [ **données déclencheurs** ](~/xamarin-forms/app-fundamentals/triggers.md#data-triggers) article.

## <a name="using-the-command-interface"></a>À l’aide de l’interface de commande

Il est possible pour une application répondre aux `Button` robinets sans traitement le `Clicked` événement. Le `Button` implémente un mécanisme de notification secondaire appelé le _commande_ ou _commandes_ interface. Cela se compose de deux propriétés :

- [`Command`](xref:Xamarin.Forms.Button.Command) de type [ `ICommand` ](xref:System.Windows.Input.ICommand), une interface définie dans le [ `System.Windows.Input` ](xref:System.Windows.Input) espace de noms.
- [`CommandParameter`](xref:Xamarin.Forms.Button.CommandParameter) propriété de type [ `Object` ](xref:System.Object).

Cette approche convient particulièrement dans le cadre de liaison de données et en particulier lors de l’implémentation de l’architecture Model-View-ViewModel (MVVM). Ces rubriques sont présentées dans les articles [une liaison de données](~/xamarin-forms/app-fundamentals/data-binding/index.md), [à partir des liaisons de données à MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md), et [MVVM](~/xamarin-forms/enterprise-application-patterns/mvvm.md).

Dans une application MVVM, le ViewModel définit les propriétés de type `ICommand` qui sont alors connectés à le XAML `Button` éléments avec des liaisons de données. Xamarin.Forms définit également [ `Command` ](xref:Xamarin.Forms.Command) et [ `Command<T>` ](xref:Xamarin.Forms.Command`1) des classes qui implémentent le `ICommand` interface et faciliter le ViewModel dans la définition des propriétés de type `ICommand`.

Exécution des commandes sont décrite plus en détail dans l’article [ **l’Interface de commande** ](~/xamarin-forms/app-fundamentals/data-binding/commanding.md) mais le **commande de bouton base** page dans le [  **ButtonDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos) exemple illustre l’approche de base.

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

Les deux `ICommand` propriétés sont initialisées dans le constructeur de la classe avec deux objets de type `Command`. Le `Command` constructeurs incluent une petite fonction (appelée la `execute` argument du constructeur) qui double ou divise par deux la `Number` propriété.

Le **BasicButtonCommand.xaml** fichier définit son `BindingContext` à une instance de `CommandDemoViewModel`. Le `Label` élément et deux `Button` les éléments contiennent des liaisons pour les trois propriétés dans `CommandDemoViewModel`:

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

En tant que les deux `Button` cliqués des éléments, les commandes sont exécutées, et le nombre change de valeur :

[![Commande de base du bouton](button-images/BasicButtonCommand.png "base bouton de commande")](button-images/BasicButtonCommand-Large.png#lightbox)

L’avantage de cette approche sur `Clicked` gestionnaires est que toute la logique qui impliquent la fonctionnalité de cette page se trouve dans le ViewModel plutôt que dans le fichier code-behind, atteindre une meilleure séparation de l’interface utilisateur à partir de la logique métier.

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

Les appels à la `ChangeCanExecute` méthode de `Command` sont nécessaires afin que le `Command` méthode peut appeler le `canExecute` méthode et déterminer si le `Button` doit être désactivé ou non. Avec cette modification du code, comme le nombre atteint la limite, le `Button` est désactivé :

[![Commande de bouton de base - modifiée](button-images/BasicButtonCommandModified.png "commande de bouton de base - modifiée")](button-images/BasicButtonCommandModified-Large.png#lightbox)

Il est possible pour deux ou plusieurs `Button` éléments à lier au même `ICommand` propriété. Le `Button` peuvent être distingués des éléments à l’aide de la [ `CommandParameter` ](xref:Xamarin.Forms.Button.CommandParameter) propriété du `Button`. Dans ce cas, vous souhaiterez utiliser générique [ `Command<T>` ](xref:Xamarin.Forms.Command`1) classe. Le `CommandParameter` objet est ensuite passé comme argument à la `execute` et `canExecute` méthodes. Cette technique est illustrée en détail dans le [ **base commandes** ](~/xamarin-forms/app-fundamentals/data-binding/commanding.md#basic-commanding) section de la [ **Interface de commande** ](~/xamarin-forms/app-fundamentals/data-binding/commanding.md#basic-commanding) article.

Le [ **ButtonDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos) exemple utilise également cette technique dans son `MainPage` classe. Le **MainPage.xaml** fichier contient un `Button` pour chaque page de l’exemple :

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

Le constructeur initialise la `NavigateCommand` propriété à un `Command<Type>` , car l’objet `Type` est le type de la `CommandParameter` objet définie dans le fichier XAML. Cela signifie que le `execute` méthode a un argument de type `Type` qui correspond à ce `CommandParameter` objet. La fonction instancie la page, puis accède à elle.

Notez que le constructeur se termine en définissant son `BindingContext` à lui-même. Cela est nécessaire pour les propriétés dans le fichier XAML pour lier à la `NavigateCommand` propriété.

## <a name="pressing-and-releasing-the-button"></a>En appuyant sur et de relâcher le bouton.

Outre le `Clicked` événement, `Button` définit également [ `Pressed` ](xref:Xamarin.Forms.Button.Pressed) et [ `Released` ](xref:Xamarin.Forms.Button.Released) événements. Le `Pressed` événement se produit lorsqu’un doigt appuie sur un `Button`, ou un bouton de la souris est enfoncé avec le pointeur sur le `Button`. Le `Released` événement se produit lorsque le bouton doigt ou de la souris est relâché. En règle générale, un `Clicked` événement est également déclenché en même temps que le `Released` événement, mais si le pointeur doigt ou de la souris diapositives en dehors de la surface de la `Button` avant la version, le `Clicked` événement ne peut pas se produire.

Le `Pressed` et `Released` événements ne sont pas souvent utilisés, mais elles peuvent servir à des fins spéciales, comme illustré dans le **Press et relâchez le bouton** page. Le fichier XAML contient un `Label` et un `Button` avec les gestionnaires attachés pour la `Pressed` et `Released` événements :

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

Anime le fichier code-behind le `Label` lorsqu’un `Pressed` l’événement se produit, mais interrompt la rotation lorsqu’un `Released` événement se produit :

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

Le résultat est que le `Label` utilise uniquement un doigt est en contact avec le `Button`et s’arrête lorsque le doigt est libéré :

[![Appuyez sur et relâchez le bouton](button-images/PressAndReleaseButton.png "appuyez et relâchez le bouton")](button-images/PressAndReleaseButton-Large.png)

Ce type de comportement a des applications pour les jeux : Un doigt détenu sur un `Button` peut faire passer un objet à l’écran dans une direction particulière.

<a name="button-appearance" />

## <a name="button-appearance"></a>Apparence du bouton

Le `Button` hérite ou définit plusieurs propriétés qui affectent son apparence :

- [`TextColor`](xref:Xamarin.Forms.Button.TextColor) est la couleur de la `Button` texte
- [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) est la couleur d’arrière-plan pour que le texte
- [`BorderColor`](xref:Xamarin.Forms.Button.BorderColor) est la couleur d’une zone qui entoure le `Button`
- [`FontFamily`](xref:Xamarin.Forms.Button.FontFamily) la famille de polices est utilisée pour le texte
- [`FontSize`](xref:Xamarin.Forms.Button.FontSize) est la taille du texte
- [`FontAttributes`](xref:Xamarin.Forms.Button.FontAttributes) Indique si le texte est en italique ou en gras
- [`BorderWidth`](xref:Xamarin.Forms.Button.BorderWidth) correspond à la largeur de la bordure
- [`CornerRadius`](xref:Xamarin.Forms.Button.CornerRadius) est le rayon d’angle de la `Button`

> [!NOTE]
> Le `Button` classe a également [ `Margin` ](xref:Xamarin.Forms.View.Margin) et [ `Padding` ](xref:Xamarin.Forms.Button.Padding) propriétés qui contrôlent le comportement de disposition de la `Button`. Pour plus d’informations, consultez [marge et marge intérieure](~/xamarin-forms/user-interface/layouts/margin-and-padding.md).

Les effets de six de ces propriétés (à l’exclusion `FontFamily` et `FontAttributes`) sont décrites dans le **apparence du bouton** page. Une autre propriété, [ `Image` ](xref:Xamarin.Forms.Button.ImageSource), est décrite dans la section [ **à l’aide de bitmaps avec le bouton**](#image-button).

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

Le `Button` en haut de la page a ses trois `Color` propriétés liées aux `Picker` éléments en bas de la page. Les éléments dans le `Picker` éléments sont des couleurs à partir de la `NamedColor` classe inclus dans le projet. Trois `Slider` les éléments contiennent des liaisons bidirectionnelles pour les `FontSize`, `BorderWidth`, et `CornerRadius` propriétés de la `Button`.

Ce programme vous permet de faire des essais avec les combinaisons de toutes ces propriétés :

[![Bouton apparence](button-images/ButtonAppearance.png "bouton apparence")](button-images/ButtonAppearance-Large.png)

Pour voir les `Button` bordure, vous devez définir un `BorderColor` une valeur autre que `Default`et le `BorderWidth` en une valeur positive.

Sur iOS, vous remarquerez que les largeurs de bordure épaisse empiéter à l’intérieur de la `Button` et interférer avec l’affichage du texte. Si vous choisissez d’utiliser une bordure avec un iOS `Button`, vous voudrez probablement commence et se termine le `Text` propriété avec des espaces pour conserver sa visibilité.

Sur UWP, en sélectionnant un `CornerRadius` qui dépasse la moitié de la hauteur de la `Button` lève une exception.

## <a name="button-visual-states"></a>États visuels du bouton

[`Button`](xref:Xamarin.Forms.Button) a un `Pressed` [ `VisualState` ](xref:Xamarin.Forms.VisualState) qui peut être utilisé pour lancer un plus grand changement pour le `Button` lorsque vous appuyez sur par l’utilisateur, à condition qu’il soit activé.

L’exemple XAML suivant montre comment définir un état visuel pour le `Pressed` état :

```xaml
<Button Text="Click me!"
        ...>
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">
            <VisualState x:Name="Normal">
                <VisualState.Setters>
                    <Setter Property="Scale"
                            Value="1" />
                </VisualState.Setters>
            </VisualState>

            <VisualState x:Name="Pressed">
                <VisualState.Setters>
                    <Setter Property="Scale"
                            Value="0.8" />
                </VisualState.Setters>
            </VisualState>

        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Button>
```

Le `Pressed` [ `VisualState` ](xref:Xamarin.Forms.VisualState) Spécifie que quand le [ `Button` ](xref:Xamarin.Forms.Button) est enfoncé, son [ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale) propriété va être changée de son valeur par défaut de 1 à 0,8. Le `Normal` `VisualState` Spécifie que quand le `Button` est dans un état normal, son `Scale` propriété est définie sur 1. Par conséquent, l’effet global est que lorsque le `Button` est enfoncé, il est remises à l’échelle pour être légèrement plus petits et lorsque le `Button` est publié, il est remises à l’échelle à sa taille par défaut.

Pour plus d’informations sur les états visuels, consultez [le Gestionnaire d’état visuel Xamarin.Forms](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="creating-a-toggle-button"></a>Création d’un bouton bascule

Il est possible d’effectuer une `Button` sous-classe afin qu’elle fonctionne comme un commutateur d’activation : Appuyez une fois sur le bouton pour activer ou désactiver le bouton, puis appuyez dessus pour le désactiver.

Ce qui suit `ToggleButton` dérive de la classe `Button` et définit un événement nommé `Toggled` et une propriété booléenne nommée `IsToggled`. Voici les deux mêmes propriétés définies par le Xamarin.Forms [ `Switch` ](xref:Xamarin.Forms.Switch):

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

Le `ToggleButton` constructeur attache un gestionnaire à la `Clicked` événement afin qu’elle peut modifier la valeur de la `IsToggled` propriété. Le `OnIsToggledChanged` méthode déclenche la `Toggled` événement.

La dernière ligne de la `OnIsToggledChanged` méthode appelle la méthode statique `VisualStateManager.GoToState` méthode avec le texte de deux chaînes « ToggledOn » et « ToggledOff ». Vous trouverez sur cette méthode et la façon dont votre application peut répondre à des états visuels dans l’article [ **le Gestionnaire d’état visuel Xamarin.Forms**](~/xamarin-forms/user-interface/visual-state-manager.md).

Étant donné que `ToggleButton` effectue l’appel à `VisualStateManager.GoToState`, la classe elle-même n’a pas besoin d’inclure d’équipements supplémentaires pour modifier l’apparence du bouton en fonction de son `IsToggled` état. Autrement dit la responsabilité du XAML qui héberge le `ToggleButton`.

Le **bascule Button Demo** page contient deux instances de `ToggleButton`, y compris le balisage de gestionnaire d’état visuel qui définit le `Text`, `BackgroundColor`, et `TextColor` du bouton en fonction de l’état visuel :

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

Le `Toggled` sont des gestionnaires d’événements dans le fichier code-behind. Ils sont responsables de paramètre le `FontAttributes` propriété de la `Label` selon l’état des boutons :

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

Voici le programme en cours d’exécution sur iOS, Android et UWP :

[![Activer/désactiver Button Demo](button-images/ToggleButtonDemo.png "activer/désactiver Button Demo")](button-images/ToggleButtonDemo-Large.png#lightbox)

<a name="image-button" />

## <a name="using-bitmaps-with-buttons"></a>À l’aide de bitmaps avec des boutons

Le `Button` classe définit un [ `ImageSource` ](xref:Xamarin.Forms.Button.Image) propriété qui vous permet d’afficher une image bitmap sur le `Button`, seul ou conjointement avec le texte. Vous pouvez également spécifier comment le texte et l’image sont organisés.

La `ImageSource` propriété est de type [`ImageSource`](xref:Xamarin.Forms.ImageSource), ce qui signifie que les bitmaps peuvent être chargées à partir d’un fichier, d’une ressource incorporée, d’un URI ou d’un flux.

Chaque plateforme prise en charge par Xamarin.Forms permet des images à être stockées dans plusieurs tailles pour les divers périphériques de l’application peut s’exécuter sur les résolutions pixels différentes. Ces bitmaps plusieurs sont nommés ou stockés de manière à ce que le système d’exploitation peut choisir la meilleure correspondance pour le périphérique vidéo résolution d’affichage.

Pour une image bitmap sur un `Button`, la taille optimale est généralement entre 32 et 64 unités indépendantes du périphérique, selon la taille que vous souhaitez qu’elle soit. Les images utilisées dans cet exemple sont basées sur une taille de 48 unités indépendantes du périphérique.

Dans le projet iOS, le **ressources** dossier contient trois tailles de cette image :

- Une bitmap de carré de 48 pixels stockée en tant que **/Resources/MonkeyFace.png**
- Une image bitmap carrée 96 pixels stockée en tant que **/Resource/MonkeyFace@2x.png**
- Une image bitmap carrée 144-pixel stockée en tant que **/Resource/MonkeyFace@3x.png**

Tous les trois bitmaps ont eu un **Action de génération** de **BundleResource**.

Pour le projet Android, les bitmaps toutes avoir le même nom, mais ils sont stockés dans des sous-dossiers différents de la **ressources** dossier :

- Une image bitmap carrée 72 pixels stockée en tant que **/Resources/drawable-hdpi/MonkeyFace.png**
- Une image bitmap carrée 96 pixels stockée en tant que **/Resources/drawable-xhdpi/MonkeyFace.png**
- Une image bitmap carrée 144-pixel stockée en tant que **/Resources/drawable-xxhdpi/MonkeyFace.png**
- Une image bitmap carrée 192 pixels stockée en tant que **/Resources/drawable-xxxhdpi/MonkeyFace.png**

Il a été attribuées un **Action de génération** de **AndroidResource**.

Dans le projet UWP, bitmaps pouvant figurer n’importe où dans le projet, mais ils sont généralement stockés dans un dossier personnalisé ou le **actifs** dossier existant. Le projet UWP contient ces bitmaps :

- Une bitmap de carré de 48 pixels stockée en tant que **/Assets/MonkeyFace.scale-100.png**
- Une image bitmap carrée 96 pixels stockée en tant que **/Assets/MonkeyFace.scale-200.png**
- Une image bitmap carrée 192 pixels stockée en tant que **/Assets/MonkeyFace.scale-400.png**

Ils ont été donnés un **Action de génération** de **contenu**.

Vous pouvez spécifier comment la `Text` et `ImageSource` propriétés sont organisées sur le `Button` à l’aide de la [ `ContentLayout` ](xref:Xamarin.Forms.Button.ContentLayout) propriété du `Button`. Cette propriété est de type [ `ButtonContentLayout` ](xref:Xamarin.Forms.Button.ButtonContentLayout), qui est une classe incorporée dans `Button`. Le [constructeur](xref:Xamarin.Forms.Button.ButtonContentLayout.%23ctor(Xamarin.Forms.Button.ButtonContentLayout.ImagePosition,System.Double)) a deux arguments :

- Un membre de la [ `ImagePosition` ](xref:Xamarin.Forms.Button.ButtonContentLayout.ImagePosition) énumération : `Left`, `Top`, `Right`, ou `Bottom` indiquant comment l’image bitmap s’affiche par rapport au texte.
- Un `double` valeur pour l’espacement entre le bitmap et le texte.

Les valeurs par défaut sont `Left` et 10 unités. Deux propriétés en lecture seule de `ButtonContentLayout` nommé [ `Position` ](xref:Xamarin.Forms.Button.ButtonContentLayout.Position) et [ `Spacing` ](xref:Xamarin.Forms.Button.ButtonContentLayout.Spacing) fournissent les valeurs de ces propriétés.

Dans le code, vous pouvez créer un `Button` et définir le `ContentLayout` propriété comme suit :

```csharp
Button button = new Button
{
    Text = "button text",
    ImageSource = new FileImageSource
    {
        File = "image filename"
    },
    ContentLayout = new Button.ButtonContentLayout(Button.ButtonContentLayout.ImagePosition.Right, 20)
};
```

Dans XAML, vous devez spécifier uniquement le membre d’énumération, ou l’espacement, ou à la fois dans n’importe quel ordre séparant par des virgules :

```xaml
<Button Text="button text"
        ImageSource="image filename"
        ContentLayout="Right, 20" />
```

Le **Image Button Demo** page utilise `OnPlatform` pour spécifier les noms de fichiers différents pour les fichiers bitmap de l’iOS, Android et UWP. Si vous souhaitez utiliser le même nom de fichier pour chaque plateforme et évitez d’utiliser `OnPlatform`, vous aurez besoin stocker les bitmaps UWP dans le répertoire racine du projet.

La première `Button` sur le **Image Button Demo** page définit le `Image` propriété mais pas le `Text` propriété :

```xaml
<Button>
    <Button.ImageSource>
        <OnPlatform x:TypeArguments="ImageSource">
            <On Platform="iOS, Android" Value="MonkeyFace.png" />
            <On Platform="UWP" Value="Assets/MonkeyFace.png" />
        </OnPlatform>
    </Button.ImageSource>
</Button>
```

Si les bitmaps UWP sont stockés dans le répertoire racine du projet, ce balisage peut être considérablement simplifié :

```xaml
<Button ImageSource="MonkeyFace.png" />
```

Pour éviter un grand nombre de balises et répétitives dans le **ImageButtonDemo.xaml** fichier implicite `Style` est également défini pour définir le `ImageSource` propriété. Cela `Style` est appliquée automatiquement à cinq autres `Button` éléments. Voici le fichier XAML complet :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ButtonDemos.ImageButtonDemoPage">

    <FlexLayout Direction="Column"
                JustifyContent="SpaceEvenly"
                AlignItems="Center">

        <FlexLayout.Resources>
            <Style TargetType="Button">
                <Setter Property="ImageSource">
                    <OnPlatform x:TypeArguments="ImageSource">
                        <On Platform="iOS, Android" Value="MonkeyFace.png" />
                        <On Platform="UWP" Value="Assets/MonkeyFace.png" />
                    </OnPlatform>
                </Setter>
            </Style>
        </FlexLayout.Resources>

        <Button>
            <Button.ImageSource>
                <OnPlatform x:TypeArguments="ImageSource">
                    <On Platform="iOS, Android" Value="MonkeyFace.png" />
                    <On Platform="UWP" Value="Assets/MonkeyFace.png" />
                </OnPlatform>
            </Button.ImageSource>
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

Les quatre finales `Button` les éléments utilisent le `ContentLayout` propriété pour spécifier une position et l’espacement du texte et image bitmap :

[![Image Button Demo](button-images/ImageButtonDemo.png "Image Button Demo")](button-images/ImageButtonDemo-Large.png#lightbox)

Vous avez maintenant vu les différentes méthodes que vous pouvez gérer `Button` événements et modifiez le `Button` apparence.

## <a name="related-links"></a>Liens connexes

- [Exemple de ButtonDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos)
- [Bouton API](xref:Xamarin.Forms.Button)
