---
title: Bouton de Xamarin.Forms
description: Le bouton répond à un cliquez ou appuyez sur dirigeant d’une application à exécuter une tâche particulière.
ms.prod: xamarin
ms.assetid: 62CAEB63-0800-44F4-9B8C-EE632138C2F5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/04/2019
ms.openlocfilehash: f82d590213076f349b21ebdee2832f2bf474d2f2
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2020
ms.locfileid: "78912113"
---
# <a name="xamarinforms-button"></a>Bouton de Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos)

_Le bouton répond à un TAP ou à un clic qui indique à une application d’effectuer une tâche particulière._

Le [`Button`](xref:Xamarin.Forms.Button) est le contrôle interactif le plus fondamental dans tous les Xamarin. Forms. La `Button` affiche généralement une chaîne de texte brève indiquant une commande, mais elle peut également afficher une image bitmap ou une combinaison de texte et d’image. L’utilisateur appuie sur le `Button` avec un doigt ou clique dessus avec la souris pour lancer la commande.

La plupart des rubriques présentées ci-dessous correspondent aux pages de l’exemple [**ButtonDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos) .

## <a name="handling-button-clicks"></a>Gestion bouton clique sur

`Button` définit un événement [`Clicked`](xref:Xamarin.Forms.Button.Clicked) qui est déclenché quand l’utilisateur appuie sur le `Button` avec un pointeur Finger ou Mouse. L’événement est déclenché lorsque le bouton de doigt ou de la souris est relâché à partir de la surface du `Button`. La propriété [`IsEnabled`](xref:Xamarin.Forms.VisualElement.IsEnabled) de l' `Button` doit avoir la valeur `true` pour qu’il réponde aux pressions.

La page de **clic du bouton de base** dans l’exemple [**ButtonDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos) montre comment instancier un `Button` en XAML et gérer son événement `Clicked`. Le fichier **BasicButtonClickPage. Xaml** contient un `StackLayout` avec une `Label` et une `Button`:

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

Le `Button` tend à occuper tout l’espace qui lui est autorisé. Par exemple, si vous ne définissez pas la propriété `HorizontalOptions` de `Button` sur une valeur autre que `Fill`, le `Button` occupera la largeur complète de son parent.

Par défaut, le `Button` est rectangulaire, mais vous pouvez lui attribuer des angles arrondis à l’aide de la propriété [`CornerRadius`](xref:Xamarin.Forms.Button.CornerRadius) , comme décrit ci-dessous dans l' [**apparence du bouton**](#button-appearance).

La propriété [`Text`](xref:Xamarin.Forms.Button.Text) spécifie le texte qui apparaît dans l’élément `Button`. L’événement [`Clicked`](xref:Xamarin.Forms.Button.Clicked) est défini sur un gestionnaire d’événements nommé `OnButtonClicked`. Ce gestionnaire se trouve dans le fichier code-behind, **BasicButtonClickPage.Xaml.cs**:

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

Lorsque le `Button` est frappé, la méthode `OnButtonClicked` s’exécute. L’argument `sender` est l’objet `Button` responsable de cet événement. Vous pouvez l’utiliser pour accéder à l’objet `Button`, ou pour faire la distinction entre plusieurs objets `Button` partageant le même `Clicked` événement.

Ce gestionnaire de `Clicked` particulier appelle une fonction d’animation qui fait pivoter le `Label` de 360 degrés en 1000 millisecondes. Voici le programme en cours d’exécution sur des appareils iOS et Android et comme une application de plateforme universelle Windows (UWP) sur le bureau Windows 10 :

[![Clic sur le bouton de base](button-images/BasicButtonClick.png "Clic sur le bouton de base")](button-images/BasicButtonClick-Large.png#lightbox "Clic sur le bouton de base")

Notez que la méthode `OnButtonClicked` comprend le modificateur `async`, car `await` est utilisé dans le gestionnaire d’événements. Un gestionnaire d’événements `Clicked` requiert le modificateur `async` uniquement si le corps du gestionnaire utilise `await`.

Chaque plateforme restitue la `Button` de manière spécifique. Dans la section [**apparence du bouton**](#button-appearance) , vous verrez comment définir des couleurs et rendre visible la bordure de `Button` pour des apparences plus personnalisées. `Button` implémente l’interface [`IFontElement`](xref:Xamarin.Forms.Internals.IFontElement) , il comprend les propriétés [`FontFamily`](xref:Xamarin.Forms.Button.FontFamily), [`FontSize`](xref:Xamarin.Forms.Button.FontSize)et [`FontAttributes`](xref:Xamarin.Forms.Button.FontAttributes) .

## <a name="creating-a-button-in-code"></a>Création d’un bouton dans le code

Il est courant d’instancier un `Button` en XAML, mais vous pouvez également créer un `Button` dans le code. Cela peut être pratique lorsque votre application doit créer plusieurs boutons basés sur des données qui sont énumérables avec une boucle de `foreach`.

La page de **clic du bouton code** montre comment créer une page fonctionnellement équivalente à la page de **clic du bouton** de base C#, mais entièrement dans :

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

Tout est fait dans le constructeur de la classe. Étant donné que le gestionnaire de `Clicked` n’est qu’une seule instruction, il peut être attaché à l’événement très simplement :

```csharp
button.Clicked += async (sender, args) => await label.RelRotateTo(360, 1000);
```

Bien entendu, vous pouvez également définir le gestionnaire d’événements en tant que méthode distincte (tout comme la méthode `OnButtonClick` dans un **clic sur le bouton de base**) et attacher cette méthode à l’événement :

```csharp
button.Clicked += OnButtonClicked;
```

## <a name="disabling-the-button"></a>Désactivation du bouton

Parfois, une application se trouve dans un état particulier où un clic de `Button` spécifique n’est pas une opération valide. Dans ce cas, le `Button` doit être désactivé en affectant à sa propriété `IsEnabled` la valeur `false`. L’exemple classique est un contrôle de `Entry` pour un nom de fichier accompagné d’un `Button`de fichier ouvert : le `Button` doit être activé uniquement si du texte a été tapé dans le `Entry`.
Vous pouvez utiliser une `DataTrigger` pour cette tâche, comme indiqué dans l’article [**déclencheurs de données**](~/xamarin-forms/app-fundamentals/triggers.md#data-triggers) .

## <a name="using-the-command-interface"></a>À l’aide de l’interface de commande

Une application peut répondre à `Button` taraudages sans gérer l’événement `Clicked`. Le `Button` implémente un autre mécanisme de notification appelé _commande_ ou interface de _commande_ . Cela se compose de deux propriétés :

- [`Command`](xref:Xamarin.Forms.Button.Command) de type [`ICommand`](xref:System.Windows.Input.ICommand), une interface définie dans l’espace de noms [`System.Windows.Input`](xref:System.Windows.Input) .
- [`CommandParameter`](xref:Xamarin.Forms.Button.CommandParameter) propriété de type [`Object`](xref:System.Object).

Cette approche convient particulièrement dans le cadre de liaison de données et en particulier lors de l’implémentation de l’architecture Model-View-ViewModel (MVVM). Ces rubriques sont décrites dans les articles [liaison de données](~/xamarin-forms/app-fundamentals/data-binding/index.md), [à partir des liaisons de données à MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)et [MVVM](~/xamarin-forms/enterprise-application-patterns/mvvm.md).

Dans une application MVVM, le ViewModel définit des propriétés de type `ICommand` qui sont ensuite connectées aux éléments XAML `Button` avec des liaisons de données. Xamarin. Forms définit également les classes [`Command`](xref:Xamarin.Forms.Command) et [`Command<T>`](xref:Xamarin.Forms.Command`1) qui implémentent l’interface `ICommand` et aident le ViewModel à définir des propriétés de type `ICommand`.

La commande est décrite plus en détail dans l’article [**interface de commande**](~/xamarin-forms/app-fundamentals/data-binding/commanding.md) , mais la page de **commandes du bouton de base** dans l’exemple [**ButtonDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos) illustre l’approche de base.

La classe `CommandDemoViewModel` est un ViewModel très simple qui définit une propriété de type `double` nommée `Number`et deux propriétés de type `ICommand` nommées `MultiplyBy2Command` et `DivideBy2Command`:

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

Les deux propriétés de `ICommand` sont initialisées dans le constructeur de la classe avec deux objets de type `Command`. Les constructeurs `Command` incluent une petite fonction (appelée argument de constructeur `execute`) qui double ou divise la propriété `Number`.

Le fichier **BasicButtonCommand. Xaml** définit son `BindingContext` sur une instance de `CommandDemoViewModel`. L’élément `Label` et deux éléments `Button` contiennent des liaisons aux trois propriétés dans `CommandDemoViewModel`:

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

À mesure que les deux éléments `Button` sont frappés, les commandes sont exécutées et le nombre change de valeur :

[![Bouton de base, commande](button-images/BasicButtonCommand.png "Bouton de base, commande")](button-images/BasicButtonCommand-Large.png#lightbox)

L’avantage de cette approche par rapport à `Clicked` gestionnaires est que toute la logique impliquant les fonctionnalités de cette page se trouve dans le ViewModel et non dans le fichier code-behind, ce qui améliore la séparation de l’interface utilisateur et de la logique métier.

Il est également possible que les objets `Command` contrôlent l’activation et la désactivation des éléments `Button`. Supposons, par exemple, que vous souhaitiez limiter la plage de valeurs de nombres comprises entre 2<sup>10</sup> et 2<sup>&ndash;10</sup>. Vous pouvez ajouter une autre fonction au constructeur (appelé `canExecute` argument) qui retourne `true` si la `Button` doit être activée. Voici la modification apportée au constructeur `CommandDemoViewModel` :

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

Les appels à la méthode `ChangeCanExecute` de `Command` sont nécessaires afin que la méthode `Command` puisse appeler la méthode `canExecute` et déterminer si la `Button` doit être désactivée ou non. Avec ce code modifié, étant donné que le nombre atteint la limite, le `Button` est désactivé :

[![Bouton de base-modification de la commande](button-images/BasicButtonCommandModified.png "Bouton de base-modification de la commande")](button-images/BasicButtonCommandModified-Large.png#lightbox)

Il est possible que deux ou plusieurs éléments `Button` soient liés à la même propriété `ICommand`. Les éléments `Button` peuvent être distingués à l’aide de la propriété [`CommandParameter`](xref:Xamarin.Forms.Button.CommandParameter) de `Button`. Dans ce cas, vous souhaiterez utiliser la classe de [`Command<T>`](xref:Xamarin.Forms.Command`1) générique. L’objet `CommandParameter` est ensuite passé comme argument aux méthodes `execute` et `canExecute`. Cette technique est décrite en détail dans la section relative aux [**commandes de base**](~/xamarin-forms/app-fundamentals/data-binding/commanding.md#basic-commanding) de l’article sur l' [**interface de commande**](~/xamarin-forms/app-fundamentals/data-binding/commanding.md#basic-commanding) .

L’exemple [**ButtonDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos) utilise également cette technique dans sa classe `MainPage`. Le fichier **MainPage. Xaml** contient un `Button` pour chaque page de l’exemple :

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

Chaque `Button` a sa propriété `Command` liée à une propriété nommée `NavigateCommand`et la `CommandParameter` est définie sur un objet [`Type`](xref:System.Type) correspondant à l’une des classes page du projet.

Cette `NavigateCommand` propriété est de type `ICommand` et est définie dans le fichier code-behind :

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

Le constructeur initialise la propriété `NavigateCommand` à un objet `Command<Type>`, car `Type` est le type de l’objet `CommandParameter` défini dans le fichier XAML. Cela signifie que la méthode `execute` a un argument de type `Type` qui correspond à cet objet `CommandParameter`. La fonction instancie la page, puis accède à elle.

Notez que le constructeur se termine en définissant son `BindingContext` sur lui-même. Cela est nécessaire pour que les propriétés du fichier XAML soient liées à la propriété `NavigateCommand`.

## <a name="pressing-and-releasing-the-button"></a>En appuyant sur et de relâcher le bouton.

Outre l’événement `Clicked`, `Button` définit également les événements [`Pressed`](xref:Xamarin.Forms.Button.Pressed) et [`Released`](xref:Xamarin.Forms.Button.Released). L’événement `Pressed` se produit lorsqu’un doigt appuie sur un `Button`, ou lorsqu’un bouton de la souris est enfoncé avec le pointeur positionné sur le `Button`. L’événement `Released` se produit lorsque le bouton de doigt ou de la souris est relâché. En règle générale, un événement `Clicked` est également déclenché en même temps que l’événement `Released`, mais si le pointeur de la souris ou du doigt s’éloigne de la surface de l' `Button` avant d’être relâché, l’événement `Clicked` peut ne pas se produire.

Les événements `Pressed` et `Released` ne sont pas souvent utilisés, mais ils peuvent être utilisés à des fins spécifiques, comme illustré dans la page du **bouton appuyer et libérer** . Le fichier XAML contient un `Label` et un `Button` avec des gestionnaires attachés pour les événements `Pressed` et `Released` :

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

Le fichier code-behind anime l' `Label` lorsqu’un événement `Pressed` se produit, mais interrompt la rotation lorsqu’un événement `Released` se produit :

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

Le résultat est que le `Label` pivote uniquement lorsqu’un doigt est en contact avec le `Button`et s’arrête lorsque le doigt est relâché :

[![Bouton appuyer et relâcher](button-images/PressAndReleaseButton.png "Bouton appuyer et relâcher")](button-images/PressAndReleaseButton-Large.png)

Ce type de comportement a des applications pour les jeux : un doigt maintenu sur un `Button` peut faire passer un objet à l’écran dans une direction particulière.

<a name="button-appearance" />

## <a name="button-appearance"></a>Apparence du bouton

Le `Button` hérite ou définit plusieurs propriétés qui affectent son apparence :

- [`TextColor`](xref:Xamarin.Forms.Button.TextColor) est la couleur du texte `Button`
- [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) est la couleur de l’arrière-plan de ce texte
- [`BorderColor`](xref:Xamarin.Forms.Button.BorderColor) est la couleur d’une zone entourant le `Button`
- [`FontFamily`](xref:Xamarin.Forms.Button.FontFamily) est la famille de polices utilisée pour le texte
- [`FontSize`](xref:Xamarin.Forms.Button.FontSize) est la taille du texte
- [`FontAttributes`](xref:Xamarin.Forms.Button.FontAttributes) indique si le texte est en italique ou en gras
- [`BorderWidth`](xref:Xamarin.Forms.Button.BorderWidth) est la largeur de la bordure
- [`CornerRadius`](xref:Xamarin.Forms.Button.CornerRadius) est le rayon de l’angle du `Button`
- `CharacterSpacing` est l’espacement entre les caractères du texte `Button`

> [!NOTE]
> La classe `Button` possède également des propriétés [`Margin`](xref:Xamarin.Forms.View.Margin) et [`Padding`](xref:Xamarin.Forms.Button.Padding) qui contrôlent le comportement de disposition de l' `Button`. Pour plus d’informations, consultez la page [Marge et remplissage](~/xamarin-forms/user-interface/layouts/margin-and-padding.md).

Les effets de six de ces propriétés (à l’exception de `FontFamily` et `FontAttributes`) sont illustrés dans la page **apparence du bouton** . Une autre propriété, [`Image`](xref:Xamarin.Forms.Button.ImageSource), est présentée dans la section [**utilisation de bitmaps avec le bouton**](#image-button).

Toutes les vues et liaisons de données dans la page **apparence du bouton** sont définies dans le fichier XAML :

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

La `Button` en haut de la page a ses trois propriétés `Color` liées aux éléments `Picker` au bas de la page. Les éléments dans les éléments `Picker` sont des couleurs de la classe `NamedColor` incluse dans le projet. Trois éléments `Slider` contiennent des liaisons bidirectionnelles aux propriétés `FontSize`, `BorderWidth`et `CornerRadius` de l' `Button`.

Ce programme vous permet de faire des essais avec les combinaisons de toutes ces propriétés :

[![Apparence du bouton](button-images/ButtonAppearance.png "Apparence du bouton")](button-images/ButtonAppearance-Large.png)

Pour afficher la bordure de `Button`, vous devez définir un `BorderColor` autre que `Default`et le `BorderWidth` sur une valeur positive.

Sur iOS, vous remarquerez que de grandes largeurs de bordures sont à l’intérieur de la `Button` et interfèrent avec l’affichage de texte. Si vous choisissez d’utiliser une bordure avec un `Button`iOS, vous souhaiterez probablement commencer et terminer la propriété `Text` par des espaces pour conserver sa visibilité.

Sur UWP, la sélection d’un `CornerRadius` qui dépasse la moitié de la hauteur de la `Button` lève une exception.

## <a name="button-visual-states"></a>États visuels du bouton

[`Button`](xref:Xamarin.Forms.Button) a un [`VisualState`](xref:Xamarin.Forms.VisualState) `Pressed` qui peut être utilisé pour initier une modification visuelle de l' `Button` lorsqu’il est appuyé par l’utilisateur, à condition qu’il soit activé.

L’exemple de code XAML suivant montre comment définir un état visuel pour l’État `Pressed` :

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

L' `Pressed` [`VisualState`](xref:Xamarin.Forms.VisualState) spécifie que lorsque l' [`Button`](xref:Xamarin.Forms.Button) est enfoncé, sa valeur par défaut de la propriété [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) est remplacée par 0,8. L' `Normal` `VisualState` spécifie que lorsque le `Button` est dans un état normal, sa propriété `Scale` est définie sur 1. Par conséquent, l’effet global est que lorsque le `Button` est enfoncé, il est mis à l’échelle pour être légèrement plus petit, et lorsque la `Button` est libérée, elle est redimensionnée à sa taille par défaut.

Pour plus d’informations sur les États visuels, consultez [le gestionnaire d’état visuel Xamarin. Forms](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="creating-a-toggle-button"></a>Création d’un bouton bascule

Il est possible de sous-classer `Button` de manière à ce qu’il fonctionne comme un commutateur ON-OFF : Appuyez une fois sur le bouton pour basculer le bouton, puis appuyez à nouveau dessus pour le désactiver.

La classe `ToggleButton` suivante dérive de `Button` et définit un nouvel événement nommé `Toggled` et une propriété booléenne nommée `IsToggled`. Il s’agit des deux mêmes propriétés définies par l' [`Switch`](xref:Xamarin.Forms.Switch)Xamarin. Forms :

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

Le constructeur `ToggleButton` attache un gestionnaire à l’événement `Clicked` afin qu’il puisse modifier la valeur de la propriété `IsToggled`. La méthode `OnIsToggledChanged` déclenche l’événement `Toggled`.

La dernière ligne de la méthode `OnIsToggledChanged` appelle la méthode statique `VisualStateManager.GoToState` avec les deux chaînes de texte « ToggledOn » et « ToggledOff ». Vous pouvez en savoir plus sur cette méthode et sur la manière dont votre application peut répondre aux États visuels dans l’article [**Gestionnaire d’état visuel Xamarin. Forms**](~/xamarin-forms/user-interface/visual-state-manager.md).

Étant donné que `ToggleButton` effectue l’appel à `VisualStateManager.GoToState`, la classe elle-même n’a pas besoin d’inclure des fonctionnalités supplémentaires pour modifier l’apparence du bouton en fonction de son état de `IsToggled`. C’est la responsabilité du XAML qui héberge l' `ToggleButton`.

La page de **démonstration du bouton bascule** contient deux instances de `ToggleButton`, notamment le balisage du gestionnaire d’état visuel qui définit le `Text`, `BackgroundColor`et `TextColor` du bouton en fonction de l’état visuel :

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

Les gestionnaires d’événements `Toggled` se trouvent dans le fichier code-behind. Ils sont chargés de définir la propriété `FontAttributes` du `Label` en fonction de l’état des boutons :

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

[![Démo bouton bascule](button-images/ToggleButtonDemo.png "Démo bouton bascule")](button-images/ToggleButtonDemo-Large.png#lightbox)

<a name="image-button" />

## <a name="using-bitmaps-with-buttons"></a>À l’aide de bitmaps avec des boutons

La classe `Button` définit une propriété [`ImageSource`](xref:Xamarin.Forms.Button.Image) qui vous permet d’afficher une image bitmap sur le `Button`, seul ou en combinaison avec le texte. Vous pouvez également spécifier comment le texte et l’image sont organisés.

La propriété `ImageSource` est de type [`ImageSource`](xref:Xamarin.Forms.ImageSource), ce qui signifie que les bitmaps peuvent être chargées à partir d’un fichier, d’une ressource incorporée, d’un URI ou d’un flux.

> [!NOTE]
> Bien qu’un `Button` puisse charger une image GIF animée, il n’affiche que la première image de l’image GIF.

Chaque plateforme prise en charge par Xamarin.Forms permet des images à être stockées dans plusieurs tailles pour les divers périphériques de l’application peut s’exécuter sur les résolutions pixels différentes. Ces bitmaps plusieurs sont nommés ou stockés de manière à ce que le système d’exploitation peut choisir la meilleure correspondance pour le périphérique vidéo résolution d’affichage.

Pour une image bitmap sur un `Button`, la taille optimale est généralement comprise entre 32 et 64 unités indépendantes du périphérique, en fonction de la taille que vous souhaitez. Les images utilisées dans cet exemple sont basées sur une taille de 48 unités indépendantes du périphérique.

Dans le projet iOS, le dossier **ressources** contient trois tailles de cette image :

- Bitmap carrée de 48 pixels stockée en tant que **/Resources/MonkeyFace.png**
- Bitmap carrée de 96 pixels stockée en tant que **/Resource/MonkeyFace@2x.png**
- Bitmap carrée de 144 pixels stockée en tant que **/Resource/MonkeyFace@3x.png**

Les trois bitmaps ont reçu une **action** de génération **BundleResource**.

Pour le projet Android, les bitmaps ont toutes le même nom, mais elles sont stockées dans différents sous-dossiers du dossier **Resources** :

- Bitmap carrée de 72 pixels stockée en tant que **/Resources/Drawable-HDPI/MonkeyFace.png**
- Bitmap carrée de 96 pixels stockée en tant que **/Resources/Drawable-xhdpi/MonkeyFace.png**
- Bitmap carrée de 144 pixels stockée en tant que **/Resources/Drawable-xxhdpi/MonkeyFace.png**
- Bitmap carrée de 192 pixels stockée en tant que **/Resources/Drawable-xxxhdpi/MonkeyFace.png**

Vous avez reçu une **action** de génération **AndroidResource**.

Dans le projet UWP, les bitmaps peuvent être stockées n’importe où dans le projet, mais elles sont généralement stockées dans un dossier personnalisé ou dans le dossier des **ressources** existantes. Le projet UWP contient ces bitmaps :

- Bitmap carrée de 48 pixels stockée en tant que **/Assets/MonkeyFace.Scale-100.png**
- Bitmap carrée de 96 pixels stockée en tant que **/Assets/MonkeyFace.Scale-200.png**
- Bitmap carrée de 192 pixels stockée en tant que **/Assets/MonkeyFace.Scale-400.png**

Elles ont toutes été affectées à une **action de génération** de **contenu**.

Vous pouvez spécifier la façon dont les propriétés `Text` et `ImageSource` sont organisées sur le `Button` à l’aide de la propriété [`ContentLayout`](xref:Xamarin.Forms.Button.ContentLayout) de `Button`. Cette propriété est de type [`ButtonContentLayout`](xref:Xamarin.Forms.Button.ButtonContentLayout), qui est une classe incorporée dans `Button`. Le [constructeur](xref:Xamarin.Forms.Button.ButtonContentLayout.%23ctor(Xamarin.Forms.Button.ButtonContentLayout.ImagePosition,System.Double)) a deux arguments :

- Membre de l’énumération [`ImagePosition`](xref:Xamarin.Forms.Button.ButtonContentLayout.ImagePosition) : `Left`, `Top`, `Right`ou `Bottom` indiquant comment la bitmap apparaît par rapport au texte.
- Valeur `double` pour l’espacement entre l’image bitmap et le texte.

Les valeurs par défaut sont `Left` et 10 unités. Deux propriétés en lecture seule de `ButtonContentLayout` nommées [`Position`](xref:Xamarin.Forms.Button.ButtonContentLayout.Position) et [`Spacing`](xref:Xamarin.Forms.Button.ButtonContentLayout.Spacing) fournissent les valeurs de ces propriétés.

Dans le code, vous pouvez créer une `Button` et définir la propriété `ContentLayout` comme suit :

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

La page de **démonstration du bouton image** utilise `OnPlatform` pour spécifier des noms de fichier différents pour les fichiers bitmap iOS, Android et UWP. Si vous souhaitez utiliser le même nom de fichier pour chaque plateforme et éviter l’utilisation de `OnPlatform`, vous devez stocker les bitmaps UWP dans le répertoire racine du projet.

La première `Button` sur la page de **démonstration du bouton image** définit la propriété `Image`, mais pas la propriété `Text` :

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

Pour éviter un grand nombre de balises répétitives dans le fichier **ImageButtonDemo. Xaml** , un `Style` implicite est également défini pour définir la propriété `ImageSource`. Cette `Style` est appliquée automatiquement à cinq autres éléments `Button`. Voici le fichier XAML complet :

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

Les quatre derniers éléments `Button` utilisent la propriété `ContentLayout` pour spécifier la position et l’espacement du texte et de l’image bitmap :

[![Démonstration du bouton image](button-images/ImageButtonDemo.png "Démonstration du bouton image")](button-images/ImageButtonDemo-Large.png#lightbox)

Vous avez maintenant vu les différentes façons de gérer les événements de `Button` et de modifier l’apparence des `Button`.

## <a name="related-links"></a>Liens connexes

- [Exemple ButtonDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos)
- [API Button](xref:Xamarin.Forms.Button)
