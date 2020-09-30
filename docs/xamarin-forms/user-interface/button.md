---
title: Xamarin.Forms Bouton
description: Le bouton répond à un TAP ou à un clic qui indique à une application d’effectuer une tâche particulière.
ms.prod: xamarin
ms.assetid: 62CAEB63-0800-44F4-9B8C-EE632138C2F5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/21/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: fba8dcb344f79c21815a58cff0d8132967381cca
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91556735"
---
# <a name="no-locxamarinforms-button"></a>Xamarin.Forms Bouton

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos)

_Le bouton répond à un TAP ou à un clic qui indique à une application d’effectuer une tâche particulière._

[`Button`](xref:Xamarin.Forms.Button)Est le contrôle interactif le plus fondamental dans tout Xamarin.Forms . `Button`Affiche généralement une chaîne de texte brève indiquant une commande, mais elle peut également afficher une image bitmap, ou une combinaison de texte et d’image. L’utilisateur appuie sur le `Button` avec un doigt ou clique dessus avec la souris pour lancer la commande.

La plupart des rubriques présentées ci-dessous correspondent aux pages de l’exemple [**ButtonDemos**](/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos) .

## <a name="handling-button-clicks"></a>Gestion des clics de bouton

`Button` définit un [`Clicked`](xref:Xamarin.Forms.Button.Clicked) événement qui est déclenché quand l’utilisateur appuie `Button` sur le avec un pointeur Finger ou Mouse. L’événement est déclenché lorsque le bouton de doigt ou de la souris est relâché à partir de la surface du `Button` . La `Button` propriété doit avoir la [`IsEnabled`](xref:Xamarin.Forms.VisualElement.IsEnabled) valeur `true` pour qu’elle réponde aux pressions.

La page de **clic du bouton de base** dans l’exemple [**ButtonDemos**](/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos) montre comment instancier un `Button` en XAML et gérer son `Clicked` événement. Le fichier **BasicButtonClickPage. Xaml** contient un `StackLayout` avec un `Label` et un `Button` :

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

Le `Button` tend à occuper tout l’espace qui lui est autorisé. Par exemple, si vous ne définissez pas la `HorizontalOptions` propriété de `Button` sur une valeur autre que `Fill` , le `Button` occupera la largeur complète de son parent.

Par défaut, `Button` est rectangulaire, mais vous pouvez lui attribuer des angles arrondis à l’aide de la [`CornerRadius`](xref:Xamarin.Forms.Button.CornerRadius) propriété, comme décrit ci-dessous dans l' [**apparence du bouton**](#button-appearance).

La [`Text`](xref:Xamarin.Forms.Button.Text) propriété spécifie le texte qui apparaît dans le `Button` . L' [`Clicked`](xref:Xamarin.Forms.Button.Clicked) événement est défini sur un gestionnaire d’événements nommé `OnButtonClicked` . Ce gestionnaire se trouve dans le fichier code-behind, **BasicButtonClickPage.Xaml.cs**:

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

Lorsque vous appuyez sur `Button`, la méthode `OnButtonClicked` s’exécute. L' `sender` argument est l' `Button` objet responsable de cet événement. Vous pouvez l’utiliser pour accéder à l' `Button` objet, ou pour faire la distinction entre plusieurs `Button` objets qui partagent le même `Clicked` événement.

Ce `Clicked` gestionnaire particulier appelle une fonction d’animation qui fait pivoter le `Label` 360 degrés en 1000 millisecondes. Voici le programme en cours d’exécution sur les appareils iOS et Android, ainsi que sur l’application plateforme Windows universelle (UWP) sur le bureau Windows 10 :

[![Clic sur le bouton de base](button-images/BasicButtonClick.png "Clic sur le bouton de base")](button-images/BasicButtonClick-Large.png#lightbox "Clic sur le bouton de base")

Notez que la `OnButtonClicked` méthode comprend le `async` modificateur, car `await` est utilisé dans le gestionnaire d’événements. Un `Clicked` Gestionnaire d’événements requiert le `async` modificateur uniquement si le corps du gestionnaire utilise `await` .

Chaque plateforme restitue le `Button` de la manière qui lui est propre. Dans la section [**apparence du bouton**](#button-appearance) , vous verrez comment définir des couleurs et rendre la `Button` bordure visible pour des apparences plus personnalisées. `Button` implémente l' [`IFontElement`](xref:Xamarin.Forms.Internals.IFontElement) interface, de sorte qu’elle comprend les [`FontFamily`](xref:Xamarin.Forms.Button.FontFamily) [`FontSize`](xref:Xamarin.Forms.Button.FontSize) Propriétés, et [`FontAttributes`](xref:Xamarin.Forms.Button.FontAttributes) .

## <a name="creating-a-button-in-code"></a>Création d’un bouton dans le code

Il est courant d’instancier un `Button` en XAML, mais vous pouvez également créer un `Button` dans le code. Cela peut être pratique lorsque votre application doit créer plusieurs boutons basés sur des données qui sont énumérables avec une `foreach` boucle.

La page de **clic du bouton code** montre comment créer une page fonctionnellement équivalente à la page de **clic du bouton de base** , mais entièrement en C# :

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

Tout est effectué dans le constructeur de la classe. Étant donné que le `Clicked` gestionnaire n’est qu’une seule instruction, il peut être attaché à l’événement très simplement :

```csharp
button.Clicked += async (sender, args) => await label.RelRotateTo(360, 1000);
```

Bien entendu, vous pouvez également définir le gestionnaire d’événements comme une méthode distincte (tout comme la `OnButtonClick` méthode dans un **clic de bouton de base**) et attacher cette méthode à l’événement :

```csharp
button.Clicked += OnButtonClicked;
```

## <a name="disabling-the-button"></a>Désactivation du bouton

Parfois, une application se trouve dans un état particulier où un `Button` clic particulier n’est pas une opération valide. Dans ce cas, `Button` doit être désactivé en affectant à sa propriété la valeur `IsEnabled` `false` . L’exemple classique est un `Entry` contrôle pour un nom de fichier accompagné d’un fichier-Open `Button` : le `Button` doit être activé uniquement si du texte a été tapé dans le `Entry` .
Vous pouvez utiliser un `DataTrigger` pour cette tâche, comme indiqué dans l’article [**déclencheurs de données**](~/xamarin-forms/app-fundamentals/triggers.md#data-triggers) .

## <a name="using-the-command-interface"></a>Utilisation de l’interface de commande

Une application peut répondre à des `Button` pressions sans gérer l' `Clicked` événement. Le `Button` implémente un autre mécanisme de notification appelé _commande_ ou interface de _commande_ . Il s’agit de deux propriétés :

- [`Command`](xref:Xamarin.Forms.Button.Command) de type [`ICommand`](xref:System.Windows.Input.ICommand) , une interface définie dans l' [`System.Windows.Input`](xref:System.Windows.Input) espace de noms.
- [`CommandParameter`](xref:Xamarin.Forms.Button.CommandParameter) propriété de type [`Object`](xref:System.Object) .

Cette approche est particulièrement adaptée dans le cadre de la liaison de données, et en particulier lors de l’implémentation de l’architecture MVVM (Model-View-ViewModel). Ces rubriques sont décrites dans les articles [liaison de données](~/xamarin-forms/app-fundamentals/data-binding/index.md), [à partir des liaisons de données à MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)et [MVVM](~/xamarin-forms/enterprise-application-patterns/mvvm.md).

Dans une application MVVM, le ViewModel définit les propriétés de type `ICommand` qui sont ensuite connectées aux `Button` éléments XAML avec des liaisons de données. Xamarin.Forms définit également [`Command`](xref:Xamarin.Forms.Command) les [`Command<T>`](xref:Xamarin.Forms.Command`1) classes et qui implémentent l' `ICommand` interface et aident le ViewModel à définir des propriétés de type `ICommand` .

La commande est décrite plus en détail dans l’article [**interface de commande**](~/xamarin-forms/app-fundamentals/data-binding/commanding.md) , mais la page de **commandes du bouton de base** dans l’exemple [**ButtonDemos**](/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos) illustre l’approche de base.

La `CommandDemoViewModel` classe est un ViewModel très simple qui définit une propriété de type `double` nommé et `Number` deux propriétés de type `ICommand` nommées `MultiplyBy2Command` et `DivideBy2Command` :

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

Les deux `ICommand` propriétés sont initialisées dans le constructeur de la classe avec deux objets de type `Command` . Les `Command` constructeurs incluent une petite fonction (appelée argument de `execute` constructeur) qui double ou divise la `Number` propriété.

Le fichier **BasicButtonCommand. Xaml** définit son `BindingContext` sur une instance de `CommandDemoViewModel` . L' `Label` élément et deux `Button` éléments contiennent des liaisons aux trois propriétés dans `CommandDemoViewModel` :

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

À mesure que les deux `Button` éléments sont frappés, les commandes sont exécutées et le nombre change de valeur :

[![Bouton de base, commande](button-images/BasicButtonCommand.png "Bouton de base, commande")](button-images/BasicButtonCommand-Large.png#lightbox)

L’avantage de cette approche par rapport aux `Clicked` gestionnaires est que toute la logique impliquant les fonctionnalités de cette page se trouve dans le ViewModel et non dans le fichier code-behind, ce qui améliore la séparation de l’interface utilisateur de la logique métier.

Il est également possible que les `Command` objets contrôlent l’activation et la désactivation des `Button` éléments. Supposons, par exemple, que vous souhaitiez limiter la plage de valeurs de nombres comprises entre 2<sup>10</sup> et 2<sup> &ndash; 10</sup>. Vous pouvez ajouter une autre fonction au constructeur (appelé `canExecute` argument) qui retourne `true` si `Button` doit être activé. Voici la modification apportée au `CommandDemoViewModel` constructeur :

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

Les appels à la `ChangeCanExecute` méthode de `Command` sont nécessaires pour que la `Command` méthode puisse appeler la `canExecute` méthode et déterminer si la `Button` doit être désactivée ou non. Une fois ce code modifié, étant donné que le nombre atteint la limite, le `Button` est désactivé :

[![Bouton de base-modification de la commande](button-images/BasicButtonCommandModified.png "Bouton de base-modification de la commande")](button-images/BasicButtonCommandModified-Large.png#lightbox)

Il est possible que deux éléments ou plus `Button` soient liés à la même `ICommand` propriété. Les `Button` éléments peuvent être distingués à l’aide [`CommandParameter`](xref:Xamarin.Forms.Button.CommandParameter) de la propriété de `Button` . Dans ce cas, vous souhaiterez utiliser la classe générique [`Command<T>`](xref:Xamarin.Forms.Command`1) . L' `CommandParameter` objet est ensuite passé comme argument aux `execute` `canExecute` méthodes et. Cette technique est décrite en détail dans la section relative aux [**commandes de base**](~/xamarin-forms/app-fundamentals/data-binding/commanding.md#basic-commanding) de l’article sur l' [**interface de commande**](~/xamarin-forms/app-fundamentals/data-binding/commanding.md#basic-commanding) .

L’exemple [**ButtonDemos**](/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos) utilise également cette technique dans sa `MainPage` classe. Le fichier **MainPage. Xaml** contient un `Button` pour chaque page de l’exemple :

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

Chaque `Button` propriété a sa `Command` propriété liée à une propriété nommée `NavigateCommand` , et `CommandParameter` est défini sur un [`Type`](xref:System.Type) objet correspondant à l’une des classes de page dans le projet.

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

Le constructeur initialise la `NavigateCommand` propriété à un `Command<Type>` objet, car `Type` est le type de l' `CommandParameter` objet défini dans le fichier XAML. Cela signifie que la `execute` méthode a un argument de type `Type` qui correspond à cet `CommandParameter` objet. La fonction instancie la page, puis y accède.

Notez que le constructeur se termine en affectant à sa valeur `BindingContext` lui-même. Cela est nécessaire pour que les propriétés du fichier XAML soient liées à la `NavigateCommand` propriété.

## <a name="pressing-and-releasing-the-button"></a>En appuyant sur le bouton et en le relâchant

Outre l' `Clicked` événement, `Button` définit également [`Pressed`](xref:Xamarin.Forms.Button.Pressed) les [`Released`](xref:Xamarin.Forms.Button.Released) événements et. L' `Pressed` événement se produit lorsqu’un doigt appuie sur un `Button` , ou lorsqu’un bouton de la souris est enfoncé avec le pointeur positionné sur le `Button` . L' `Released` événement se produit lorsque le bouton de doigt ou de la souris est relâché. En règle générale, un `Clicked` événement est également déclenché en même temps que l' `Released` événement, mais si le pointeur de la souris ou du doigt s’éloigne de la surface du `Button` avant d’être relâché, il `Clicked` se peut que l’événement ne se produise pas.

Les `Pressed` `Released` événements et ne sont pas souvent utilisés, mais ils peuvent être utilisés à des fins spécifiques, comme illustré dans la page du **bouton appuyer et libérer** . Le fichier XAML contient un `Label` et un `Button` avec des gestionnaires attachés pour `Pressed` les `Released` événements et :

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

Le fichier code-behind anime le `Label` lorsqu’un `Pressed` événement se produit, mais interrompt la rotation lorsqu’un `Released` événement se produit :

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

Le résultat est que le `Label` pivote uniquement lorsqu’un doigt est en contact avec le `Button` , et s’arrête lorsque le doigt est relâché :

[![Bouton appuyer et relâcher](button-images/PressAndReleaseButton.png "Bouton appuyer et relâcher")](button-images/PressAndReleaseButton-Large.png)

Ce type de comportement a des applications pour les jeux : un doigt détenu sur un `Button` peut faire passer un objet à l’écran dans une direction particulière.

## <a name="button-appearance"></a>Apparence du bouton

`Button`Hérite ou définit plusieurs propriétés qui affectent son apparence :

- [`TextColor`](xref:Xamarin.Forms.Button.TextColor) couleur du `Button` texte
- [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) est la couleur de l’arrière-plan de ce texte
- [`BorderColor`](xref:Xamarin.Forms.Button.BorderColor) est la couleur d’une zone entourant le `Button`
- [`FontFamily`](xref:Xamarin.Forms.Button.FontFamily) famille de polices utilisée pour le texte
- [`FontSize`](xref:Xamarin.Forms.Button.FontSize) taille du texte
- [`FontAttributes`](xref:Xamarin.Forms.Button.FontAttributes) indique si le texte est en italique ou en gras
- [`BorderWidth`](xref:Xamarin.Forms.Button.BorderWidth) largeur de la bordure
- [`CornerRadius`](xref:Xamarin.Forms.Button.CornerRadius) est le rayon de l’angle du `Button`
- [`CharacterSpacing`](xref:Xamarin.Forms.Button.CharacterSpacing) espacement entre les caractères du `Button` texte.
- `TextTransform` détermine la casse du `Button` texte.

> [!NOTE]
> La `Button` classe possède également [`Margin`](xref:Xamarin.Forms.View.Margin) des [`Padding`](xref:Xamarin.Forms.Button.Padding) Propriétés et qui contrôlent le comportement de disposition de `Button` . Pour plus d’informations, consultez la page [Marge et remplissage](~/xamarin-forms/user-interface/layouts/margin-and-padding.md).

Les effets de six de ces propriétés ( `FontFamily` à l’exception de et `FontAttributes` ) sont illustrés dans la page **apparence du bouton** . Une autre propriété, [`Image`](xref:Xamarin.Forms.Button.ImageSource) , est décrite dans la section [**utilisation de bitmaps avec le bouton**](#using-bitmaps-with-buttons).

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

`Button`En haut de la page, les trois propriétés sont `Color` liées aux `Picker` éléments au bas de la page. Les éléments dans les `Picker` éléments sont des couleurs de la `NamedColor` classe incluse dans le projet. Trois `Slider` éléments contiennent des liaisons bidirectionnelles aux `FontSize` Propriétés, `BorderWidth` et `CornerRadius` de `Button` .

Ce programme vous permet de faire des essais avec des combinaisons de toutes ces propriétés :

[![Apparence du bouton](button-images/ButtonAppearance.png "Apparence du bouton")](button-images/ButtonAppearance-Large.png)

Pour afficher la `Button` bordure, vous devez affecter `BorderColor` à un autre nom que `Default` , et `BorderWidth` à une valeur positive.

Sur iOS, vous remarquerez que de larges largeurs de bordures sont à l’intérieur du `Button` et interfèrent avec l’affichage de texte. Si vous choisissez d’utiliser une bordure avec iOS `Button` , vous souhaiterez probablement commencer et terminer la `Text` propriété par des espaces pour conserver sa visibilité.

Sur UWP, si vous sélectionnez un `CornerRadius` qui dépasse la moitié de la hauteur de, `Button` lève une exception.

## <a name="button-visual-states"></a>États visuels du bouton

[`Button`](xref:Xamarin.Forms.Button) a un `Pressed` [`VisualState`](xref:Xamarin.Forms.VisualState) qui peut être utilisé pour initier une modification visuelle du `Button` lorsqu’il est enfoncé par l’utilisateur, à condition qu’il soit activé.

L’exemple de code XAML suivant montre comment définir un état visuel pour l' `Pressed` État :

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

`Pressed` [`VisualState`](xref:Xamarin.Forms.VisualState) Spécifie que lorsque le [`Button`](xref:Xamarin.Forms.Button) est enfoncé, sa [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) valeur par défaut de 1 à 0,8 est remplacée. `Normal` `VisualState` Spécifie que lorsque le `Button` est dans un état normal, sa `Scale` propriété prend la valeur 1. Par conséquent, l’effet global est que lorsque l' `Button` utilisateur appuie sur le, il est mis à l’échelle pour être légèrement plus petit et, lorsque le `Button` est relâché, il est redimensionné à sa taille par défaut.

Pour plus d’informations sur les États visuels, consultez [le Xamarin.Forms Gestionnaire d’état visuel](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="creating-a-toggle-button"></a>Création d’un bouton bascule

Il est possible de sous-classer de manière à ce qu' `Button` il fonctionne comme un commutateur ON-OFF : Appuyez une fois sur le bouton pour basculer le bouton, puis appuyez à nouveau dessus pour le désactiver.

La `ToggleButton` classe suivante dérive de `Button` et définit un nouvel événement nommé `Toggled` et une propriété booléenne nommée `IsToggled` . Il s’agit des deux mêmes propriétés définies par le Xamarin.Forms [`Switch`](xref:Xamarin.Forms.Switch) :

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

Le `ToggleButton` constructeur attache un gestionnaire à l' `Clicked` événement afin qu’il puisse modifier la valeur de la `IsToggled` propriété. La `OnIsToggledChanged` méthode déclenche l' `Toggled` événement.

La dernière ligne de la `OnIsToggledChanged` méthode appelle la `VisualStateManager.GoToState` méthode statique avec les deux chaînes de texte « ToggledOn » et « ToggledOff ». Vous pouvez en savoir plus sur cette méthode et sur la manière dont votre application peut répondre aux États visuels dans l’article [** Xamarin.Forms Gestionnaire d’état visuel**](~/xamarin-forms/user-interface/visual-state-manager.md).

Étant donné que `ToggleButton` effectue l’appel à `VisualStateManager.GoToState` , la classe elle-même n’a pas besoin d’inclure des fonctionnalités supplémentaires pour modifier l’apparence du bouton en fonction de son `IsToggled` État. C’est la responsabilité du XAML qui héberge le `ToggleButton` .

La page de **démonstration du bouton bascule** contient deux instances de `ToggleButton` , notamment le balisage du gestionnaire d’état visuel qui définit `Text` , `BackgroundColor` et `TextColor` du bouton en fonction de l’état visuel :

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

Les `Toggled` gestionnaires d’événements se trouvent dans le fichier code-behind. Ils sont chargés de définir la `FontAttributes` propriété de `Label` en fonction de l’état des boutons :

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

## <a name="using-bitmaps-with-buttons"></a>Utilisation de bitmaps avec des boutons

La `Button` classe définit une [`ImageSource`](xref:Xamarin.Forms.Button.Image) propriété qui vous permet d’afficher une image bitmap sur le `Button` , seul ou en combinaison avec le texte. Vous pouvez également spécifier le mode de disposition du texte et de l’image.

La `ImageSource` propriété est de type [`ImageSource`](xref:Xamarin.Forms.ImageSource) , ce qui signifie que les bitmaps peuvent être chargées à partir d’un fichier, d’une ressource incorporée, d’un URI ou d’un flux.

> [!NOTE]
> Alors qu’un `Button` peut charger un fichier GIF animé, il n’affiche que la première image de l’image GIF.

Chaque plateforme prise en charge par Xamarin.Forms permet aux images d’être stockées en plusieurs tailles pour différentes résolutions de pixels des différents appareils sur lesquels l’application peut s’exécuter. Ces bitmaps multiples sont nommées ou stockées de telle sorte que le système d’exploitation puisse choisir la meilleure correspondance pour la résolution d’affichage vidéo de l’appareil.

Pour une image bitmap sur un `Button` , la taille optimale est généralement comprise entre 32 et 64 unités indépendantes du périphérique, en fonction de la taille que vous souhaitez. Les images utilisées dans cet exemple sont basées sur une taille de 48 unités indépendantes du périphérique.

Dans le projet iOS, le dossier **ressources** contient trois tailles de cette image :

- Bitmap carrée de 48 pixels stockée en tant que **/Resources/MonkeyFace.png**
- Bitmap carrée de 96 pixels stockée en tant que **/Resource/MonkeyFace@2x.png**
- Bitmap carrée de 144 pixels stockée en tant que **/Resource/MonkeyFace@3x.png**

Les trois bitmaps ont reçu une **action** de génération **BundleResource**.

Pour le projet Android, les bitmaps ont toutes le même nom, mais elles sont stockées dans différents sous-dossiers du dossier **Resources** :

- Bitmap carrée de 72 pixels stockée en tant que **/Resources/drawable-hdpi/MonkeyFace.png**
- Bitmap carrée de 96 pixels stockée en tant que **/Resources/drawable-xhdpi/MonkeyFace.png**
- Bitmap carrée de 144 pixels stockée en tant que **/Resources/drawable-xxhdpi/MonkeyFace.png**
- Bitmap carrée de 192 pixels stockée en tant que **/Resources/drawable-xxxhdpi/MonkeyFace.png**

Vous avez reçu une **action** de génération **AndroidResource**.

Dans le projet UWP, les bitmaps peuvent être stockées n’importe où dans le projet, mais elles sont généralement stockées dans un dossier personnalisé ou dans le dossier des **ressources** existantes. Le projet UWP contient les bitmaps suivants :

- Bitmap carrée de 48 pixels stockée en tant que **/Assets/MonkeyFace.scale-100.png**
- Bitmap carrée de 96 pixels stockée en tant que **/Assets/MonkeyFace.scale-200.png**
- Bitmap carrée de 192 pixels stockée en tant que **/Assets/MonkeyFace.scale-400.png**

Elles ont toutes été affectées à une **action de génération** de **contenu**.

Vous pouvez spécifier la façon dont les `Text` `ImageSource` Propriétés et sont organisées sur le `Button` à l’aide [`ContentLayout`](xref:Xamarin.Forms.Button.ContentLayout) de la propriété de `Button` . Cette propriété est de type [`ButtonContentLayout`](xref:Xamarin.Forms.Button.ButtonContentLayout) , qui est une classe incorporée dans `Button` . Le [constructeur] (XREF : Xamarin.Forms . Button. ButtonContentLayout .% 23ctor ( Xamarin.Forms . Button. ButtonContentLayout. ImagePosition, System. double)) a deux arguments :

- Membre de l' [`ImagePosition`](xref:Xamarin.Forms.Button.ButtonContentLayout.ImagePosition) énumération : `Left` , `Top` , `Right` ou `Bottom` indiquant comment la bitmap apparaît par rapport au texte.
- `double`Valeur pour l’espacement entre l’image bitmap et le texte.

Les valeurs par défaut sont `Left` et 10 unités. Deux propriétés en lecture seule de `ButtonContentLayout` nommées [`Position`](xref:Xamarin.Forms.Button.ButtonContentLayout.Position) et [`Spacing`](xref:Xamarin.Forms.Button.ButtonContentLayout.Spacing) fournissent les valeurs de ces propriétés.

Dans le code, vous pouvez créer un `Button` et définir la propriété de la `ContentLayout` façon suivante :

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

En XAML, vous devez spécifier uniquement le membre de l’énumération, ou l’espacement, ou les deux, dans n’importe quel ordre, séparés par des virgules :

```xaml
<Button Text="button text"
        ImageSource="image filename"
        ContentLayout="Right, 20" />
```

La page de **démonstration du bouton image** utilise `OnPlatform` pour spécifier des noms de fichiers différents pour les fichiers bitmap iOS, Android et UWP. Si vous souhaitez utiliser le même nom de fichier pour chaque plateforme et éviter d’utiliser `OnPlatform` , vous devez stocker les bitmaps UWP dans le répertoire racine du projet.

La première `Button` page de **démonstration du bouton image** définit la `Image` propriété, mais pas la `Text` propriété :

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

Si les bitmaps UWP sont stockées dans le répertoire racine du projet, ce balisage peut être considérablement simplifié :

```xaml
<Button ImageSource="MonkeyFace.png" />
```

Pour éviter un grand nombre de balises répétitives dans le fichier **ImageButtonDemo. Xaml** , un implicite `Style` est également défini pour définir la `ImageSource` propriété. Cela `Style` est appliqué automatiquement à cinq autres `Button` éléments. Voici le fichier XAML complet :

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

Les quatre derniers `Button` éléments utilisent la `ContentLayout` propriété pour spécifier la position et l’espacement du texte et de l’image bitmap :

[![Démonstration du bouton image](button-images/ImageButtonDemo.png "Démonstration du bouton image")](button-images/ImageButtonDemo-Large.png#lightbox)

Vous avez maintenant vu les différentes façons dont vous pouvez gérer `Button` les événements et modifier l' `Button` apparence.

## <a name="related-links"></a>Liens connexes

- [Exemple ButtonDemos](/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos)
- [API Button](xref:Xamarin.Forms.Button)