---
titre : «partie 5. Des liaisons de données à MVVM « Description : », le modèle MVVM applique une séparation entre trois couches logicielles (l’interface utilisateur XAML, appelée vue). données sous-jacentes, appelées modèle ; et un intermédiaire entre la vue et le modèle, appelé ViewModel.
ms. Prod : xamarin ms. Custom : vidéo ms. AssetID : 48B37D44-4FB1-41B2-9A5E-6D383B041F81 ms. Technology : xamarin-Forms Author : davidbritch ms. Author : dabritch ms. Date : 10/25/2017 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="part-5-from-data-bindings-to-mvvm"></a>Partie 5. Des liaisons de données à MVVM

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)

_Le modèle d’architecture MVVM (Model-View-ViewModel) a été inventé avec XAML à l’esprit. Le modèle applique une séparation entre trois couches logicielles (l’interface utilisateur XAML, appelée vue). données sous-jacentes, appelées modèle ; et un intermédiaire entre la vue et le modèle, appelé ViewModel. La vue et le ViewModel sont souvent connectés via des liaisons de données définies dans le fichier XAML. L’BindingContext de la vue est généralement une instance du ViewModel._

## <a name="a-simple-viewmodel"></a>Un ViewModel simple

En guise d’introduction à ViewModels, commençons par regarder un programme sans un.
Vous avez vu précédemment comment définir une nouvelle déclaration d’espace de noms XML pour permettre à un fichier XAML de référencer des classes dans d’autres assemblys. Voici un programme qui définit une déclaration d’espace de noms XML pour l' `System` espace de noms :

```csharp
xmlns:sys="clr-namespace:System;assembly=netstandard"
```

Le programme peut utiliser `x:Static` pour obtenir la date et l’heure actuelles à partir de la `DateTime.Now` propriété statique et définir cette `DateTime` valeur sur le `BindingContext` sur un `StackLayout` :

```xaml
<StackLayout BindingContext="{x:Static sys:DateTime.Now}" …>
```

`BindingContext`est une propriété spéciale : quand vous définissez le `BindingContext` sur un élément, il est hérité par tous les enfants de cet élément. Cela signifie que tous les enfants de `StackLayout` ont ce même `BindingContext` et qu’ils peuvent contenir des liaisons simples aux propriétés de cet objet.

Dans le programme **DateTime à une seule capture** , deux des enfants contiennent des liaisons aux propriétés de cette `DateTime` valeur, mais deux autres enfants contiennent des liaisons qui semblent manquer un chemin de liaison. Cela signifie que la `DateTime` valeur elle-même est utilisée pour `StringFormat` :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:sys="clr-namespace:System;assembly=netstandard"
             x:Class="XamlSamples.OneShotDateTimePage"
             Title="One-Shot DateTime Page">

    <StackLayout BindingContext="{x:Static sys:DateTime.Now}"
                 HorizontalOptions="Center"
                 VerticalOptions="Center">

        <Label Text="{Binding Year, StringFormat='The year is {0}'}" />
        <Label Text="{Binding StringFormat='The month is {0:MMMM}'}" />
        <Label Text="{Binding Day, StringFormat='The day is {0}'}" />
        <Label Text="{Binding StringFormat='The time is {0:T}'}" />

    </StackLayout>
</ContentPage>
```

Le problème est que la date et l’heure sont définies une fois lorsque la page est générée pour la première fois et ne changent jamais :

[![](data-bindings-to-mvvm-images/oneshotdatetime.png "View Displaying Date and Time")](data-bindings-to-mvvm-images/oneshotdatetime-large.png#lightbox "View Displaying Date and Time")

Un fichier XAML peut afficher une horloge qui affiche toujours l’heure actuelle, mais il a besoin de code pour vous aider. Lorsque vous envisagez en termes de MVVM, le modèle et le ViewModel sont des classes écrites entièrement dans le code. La vue est souvent un fichier XAML qui référence les propriétés définies dans le ViewModel via des liaisons de données.

Un modèle approprié ignore le ViewModel, et un ViewModel approprié ignore la vue. Toutefois, il arrive souvent qu’un programmeur personnalise les types de données exposés par le ViewModel aux types de données associés à des interfaces utilisateur particulières. Par exemple, si un modèle accède à une base de données qui contient des chaînes ASCII de caractères 8 bits, le ViewModel doit effectuer une conversion entre ces chaînes en chaînes Unicode pour s’adapter à l’utilisation exclusive d’Unicode dans l’interface utilisateur.

Dans des exemples simples de MVVM (tels que ceux présentés ici), souvent il n’y a pas de modèle, et le modèle implique simplement une vue et un ViewModel liés aux liaisons de données.

Voici un ViewModel pour une horloge avec une seule propriété nommée `DateTime` , qui met à jour cette `DateTime` propriété chaque seconde :

```csharp
using System;
using System.ComponentModel;
using Xamarin.Forms;

namespace XamlSamples
{
    class ClockViewModel : INotifyPropertyChanged
    {
        DateTime dateTime;

        public event PropertyChangedEventHandler PropertyChanged;

        public ClockViewModel()
        {
            this.DateTime = DateTime.Now;

            Device.StartTimer(TimeSpan.FromSeconds(1), () =>
                {
                    this.DateTime = DateTime.Now;
                    return true;
                });
        }

        public DateTime DateTime
        {
            set
            {
                if (dateTime != value)
                {
                    dateTime = value;

                    if (PropertyChanged != null)
                    {
                        PropertyChanged(this, new PropertyChangedEventArgs("DateTime"));
                    }
                }
            }
            get
            {
                return dateTime;
            }
        }
    }
}
```

ViewModels implémente généralement l' `INotifyPropertyChanged` interface, ce qui signifie que la classe déclenche un `PropertyChanged` événement chaque fois que l’une de ses propriétés change. Le mécanisme de liaison de données dans Xamarin.Forms attache un gestionnaire à cet `PropertyChanged` événement afin qu’il puisse être averti lorsqu’une propriété est modifiée et garder la cible mise à jour avec la nouvelle valeur.

Une horloge basée sur ce ViewModel peut être aussi simple que celle-ci :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples;assembly=XamlSamples"
             x:Class="XamlSamples.ClockPage"
             Title="Clock Page">

    <Label Text="{Binding DateTime, StringFormat='{0:T}'}"
           FontSize="Large"
           HorizontalOptions="Center"
           VerticalOptions="Center">
        <Label.BindingContext>
            <local:ClockViewModel />
        </Label.BindingContext>
    </Label>
</ContentPage>
```

Notez que `ClockViewModel` est défini sur le `BindingContext` des `Label` balises d’éléments de propriété à l’aide de. Vous pouvez également instancier le `ClockViewModel` dans une `Resources` collection et le définir à l' `BindingContext` aide d’une `StaticResource` extension de balisage. Ou le fichier code-behind peut instancier le ViewModel.

L' `Binding` extension de balisage sur la `Text` propriété de `Label` met en forme la `DateTime` propriété. Voici l’affichage :

[![](data-bindings-to-mvvm-images/clock.png "View Displaying Date and Time via ViewModel")](data-bindings-to-mvvm-images/clock-large.png#lightbox "View Displaying Date and Time via ViewModel")

Il est également possible d’accéder aux propriétés individuelles de la `DateTime` propriété du ViewModel en séparant les propriétés par des points :

```xaml
<Label Text="{Binding DateTime.Second, StringFormat='{0}'}" … >
```

## <a name="interactive-mvvm"></a>MVVM interactif

MVVM est souvent utilisé avec des liaisons de données bidirectionnelles pour une vue interactive basée sur un modèle de données sous-jacent.

Voici une classe nommée `HslViewModel` qui convertit une `Color` valeur en `Hue` `Saturation` valeurs, et `Luminosity` , et vice versa :

```csharp
using System;
using System.ComponentModel;
using Xamarin.Forms;

namespace XamlSamples
{
    public class HslViewModel : INotifyPropertyChanged
    {
        double hue, saturation, luminosity;
        Color color;

        public event PropertyChangedEventHandler PropertyChanged;

        public double Hue
        {
            set
            {
                if (hue != value)
                {
                    hue = value;
                    OnPropertyChanged("Hue");
                    SetNewColor();
                }
            }
            get
            {
                return hue;
            }
        }

        public double Saturation
        {
            set
            {
                if (saturation != value)
                {
                    saturation = value;
                    OnPropertyChanged("Saturation");
                    SetNewColor();
                }
            }
            get
            {
                return saturation;
            }
        }

        public double Luminosity
        {
            set
            {
                if (luminosity != value)
                {
                    luminosity = value;
                    OnPropertyChanged("Luminosity");
                    SetNewColor();
                }
            }
            get
            {
                return luminosity;
            }
        }

        public Color Color
        {
            set
            {
                if (color != value)
                {
                    color = value;
                    OnPropertyChanged("Color");

                    Hue = value.Hue;
                    Saturation = value.Saturation;
                    Luminosity = value.Luminosity;
                }
            }
            get
            {
                return color;
            }
        }

        void SetNewColor()
        {
            Color = Color.FromHsla(Hue, Saturation, Luminosity);
        }

        protected virtual void OnPropertyChanged(string propertyName)
        {
            PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
        }
    }
}
```

Les modifications apportées aux `Hue` `Saturation` Propriétés, et `Luminosity` provoquent la modification de la `Color` propriété, et les modifications apportées à `Color` entraînent la modification des trois autres propriétés. Cela peut sembler une boucle infinie, sauf que la classe n’appelle pas l' `PropertyChanged` événement, sauf si la propriété a été modifiée. Cela met fin à la boucle de commentaires autrement non contrôlable.

Le fichier XAML suivant contient un `BoxView` dont la `Color` propriété est liée à la `Color` propriété du ViewModel, et trois `Slider` et trois `Label` vues liées aux `Hue` Propriétés, `Saturation` et `Luminosity` :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples;assembly=XamlSamples"
             x:Class="XamlSamples.HslColorScrollPage"
             Title="HSL Color Scroll Page">
    <ContentPage.BindingContext>
        <local:HslViewModel Color="Aqua" />
    </ContentPage.BindingContext>

    <StackLayout Padding="10, 0">
        <BoxView Color="{Binding Color}"
                 VerticalOptions="FillAndExpand" />

        <Label Text="{Binding Hue, StringFormat='Hue = {0:F2}'}"
               HorizontalOptions="Center" />

        <Slider Value="{Binding Hue, Mode=TwoWay}" />

        <Label Text="{Binding Saturation, StringFormat='Saturation = {0:F2}'}"
               HorizontalOptions="Center" />

        <Slider Value="{Binding Saturation, Mode=TwoWay}" />

        <Label Text="{Binding Luminosity, StringFormat='Luminosity = {0:F2}'}"
               HorizontalOptions="Center" />

        <Slider Value="{Binding Luminosity, Mode=TwoWay}" />
    </StackLayout>
</ContentPage>
```

La liaison sur chaque `Label` est la valeur par défaut `OneWay` . Elle doit uniquement afficher la valeur. Mais la liaison sur chaque `Slider` est `TwoWay` . Cela permet `Slider` à d’être initialisé à partir du ViewModel. Notez que la `Color` propriété a la valeur `Aqua` lorsque le ViewModel est instancié. Mais une modification dans le `Slider` doit également définir une nouvelle valeur pour la propriété dans le ViewModel, qui calcule ensuite une nouvelle couleur.

[![](data-bindings-to-mvvm-images/hslcolorscroll.png "MVVM using Two-Way Data Bindings")](data-bindings-to-mvvm-images/hslcolorscroll-large.png#lightbox "MVVM using Two-Way Data Bindings")

## <a name="commanding-with-viewmodels"></a>Commande avec ViewModels

Dans de nombreux cas, le modèle MVVM est limité à la manipulation d’éléments de données : les objets d’interface utilisateur dans la vue Parallel Data Objects dans le ViewModel.

Toutefois, la vue doit parfois contenir des boutons qui déclenchent diverses actions dans le ViewModel. Mais le ViewModel ne doit pas contenir `Clicked` de gestionnaires pour les boutons, car cela lierait le ViewModel à un paradigme d’interface utilisateur particulier.

Pour permettre à ViewModels d’être plus indépendant d’objets d’interface utilisateur particuliers tout en autorisant l’appel de méthodes dans le ViewModel, il existe une interface de *commande* . Cette interface de commande est prise en charge par les éléments suivants dans Xamarin.Forms :

- `Button`
- `MenuItem`
- `ToolbarItem`
- `SearchBar`
- `TextCell`(et par conséquent également `ImageCell` )
- `ListView`
- `TapGestureRecognizer`

À l’exception de l' `SearchBar` `ListView` élément et, ces éléments définissent deux propriétés :

- `Command`de type`System.Windows.Input.ICommand`
- `CommandParameter`de type`Object`

`SearchBar`Définit les `SearchCommand` `SearchCommandParameter` Propriétés et, tandis que `ListView` définit une `RefreshCommand` propriété de type `ICommand` .

L' `ICommand` interface définit deux méthodes et un événement :

- `void Execute(object arg)`
- `bool CanExecute(object arg)`
- `event EventHandler CanExecuteChanged`

Le ViewModel peut définir des propriétés de type `ICommand` . Vous pouvez ensuite lier ces propriétés à la `Command` propriété de chaque `Button` élément ou, ou peut-être une vue personnalisée qui implémente cette interface. Vous pouvez éventuellement définir la `CommandParameter` propriété pour identifier les `Button` objets individuels (ou d’autres éléments) qui sont liés à cette propriété ViewModel. En interne, le `Button` appelle la `Execute` méthode chaque fois que l’utilisateur appuie `Button` sur le, en passant à la `Execute` méthode `CommandParameter` .

La `CanExecute` méthode et l' `CanExecuteChanged` événement sont utilisés dans les cas où un `Button` Tap n’est peut-être pas valide actuellement. dans ce cas, le `Button` doit se désactiver lui-même. Le `Button` appelle `CanExecute` lorsque la `Command` propriété est définie en premier et chaque fois que l' `CanExecuteChanged` événement est déclenché. Si `CanExecute` retourne `false` la valeur, le se `Button` désactive et ne génère pas d' `Execute` appels.

Pour obtenir de l’aide sur l’ajout de commandes à votre ViewModels, Xamarin.Forms définit deux classes qui implémentent `ICommand` : `Command` et `Command<T>` où `T` est le type des arguments de `Execute` et `CanExecute` . Ces deux classes définissent plusieurs constructeurs `ChangeCanExecute` , ainsi qu’une méthode que le ViewModel peut appeler pour forcer l' `Command` objet à déclencher l' `CanExecuteChanged` événement.

Voici un ViewModel pour un clavier simple destiné à l’entrée de numéros de téléphone. Notez que la `Execute` `CanExecute` méthode et est définie comme fonctions lambda directement dans le constructeur :

```csharp
using System;
using System.ComponentModel;
using System.Windows.Input;
using Xamarin.Forms;

namespace XamlSamples
{
    class KeypadViewModel : INotifyPropertyChanged
    {
        string inputString = "";
        string displayText = "";
        char[] specialChars = { '*', '#' };

        public event PropertyChangedEventHandler PropertyChanged;

        // Constructor
        public KeypadViewModel()
        {
            AddCharCommand = new Command<string>((key) =>
                {
                    // Add the key to the input string.
                    InputString += key;
                });

            DeleteCharCommand = new Command(() =>
                {
                    // Strip a character from the input string.
                    InputString = InputString.Substring(0, InputString.Length - 1);
                },
                () =>
                {
                    // Return true if there's something to delete.
                    return InputString.Length > 0;
                });
        }

        // Public properties
        public string InputString
        {
            protected set
            {
                if (inputString != value)
                {
                    inputString = value;
                    OnPropertyChanged("InputString");
                    DisplayText = FormatText(inputString);

                    // Perhaps the delete button must be enabled/disabled.
                    ((Command)DeleteCharCommand).ChangeCanExecute();
                }
            }

            get { return inputString; }
        }

        public string DisplayText
        {
            protected set
            {
                if (displayText != value)
                {
                    displayText = value;
                    OnPropertyChanged("DisplayText");
                }
            }
            get { return displayText; }
        }

        // ICommand implementations
        public ICommand AddCharCommand { protected set; get; }

        public ICommand DeleteCharCommand { protected set; get; }

        string FormatText(string str)
        {
            bool hasNonNumbers = str.IndexOfAny(specialChars) != -1;
            string formatted = str;

            if (hasNonNumbers || str.Length < 4 || str.Length > 10)
            {
            }
            else if (str.Length < 8)
            {
                formatted = String.Format("{0}-{1}",
                                          str.Substring(0, 3),
                                          str.Substring(3));
            }
            else
            {
                formatted = String.Format("({0}) {1}-{2}",
                                          str.Substring(0, 3),
                                          str.Substring(3, 3),
                                          str.Substring(6));
            }
            return formatted;
        }

        protected void OnPropertyChanged(string propertyName)
        {
            PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
        }
    }
}
```

Ce ViewModel suppose que la `AddCharCommand` propriété est liée à la `Command` propriété de plusieurs boutons (ou tout autre qui a une interface de commande), chacun d’entre eux étant identifié par le `CommandParameter` . Ces boutons ajoutent des caractères à une `InputString` propriété, qui est ensuite mis en forme comme un numéro de téléphone pour la `DisplayText` propriété.

Il existe également une deuxième propriété de type `ICommand` nommée `DeleteCharCommand` . Cela est lié à un bouton d’espacement, mais le bouton doit être désactivé s’il n’y a aucun caractère à supprimer.

Le clavier suivant n’est pas aussi visuellement sophistiqué que possible. Au lieu de cela, le balisage a été réduit au minimum pour illustrer plus clairement l’utilisation de l’interface de commande :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples;assembly=XamlSamples"
             x:Class="XamlSamples.KeypadPage"
             Title="Keypad Page">

    <Grid HorizontalOptions="Center"
          VerticalOptions="Center">
        <Grid.BindingContext>
            <local:KeypadViewModel />
        </Grid.BindingContext>

        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="80" />
            <ColumnDefinition Width="80" />
            <ColumnDefinition Width="80" />
        </Grid.ColumnDefinitions>

        <!-- Internal Grid for top row of items -->
        <Grid Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="3">
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="*" />
                <ColumnDefinition Width="Auto" />
            </Grid.ColumnDefinitions>

            <Frame Grid.Column="0"
                   OutlineColor="Accent">
                <Label Text="{Binding DisplayText}" />
            </Frame>

            <Button Text="&#x21E6;"
                    Command="{Binding DeleteCharCommand}"
                    Grid.Column="1"
                    BorderWidth="0" />
        </Grid>

        <Button Text="1"
                Command="{Binding AddCharCommand}"
                CommandParameter="1"
                Grid.Row="1" Grid.Column="0" />

        <Button Text="2"
                Command="{Binding AddCharCommand}"
                CommandParameter="2"
                Grid.Row="1" Grid.Column="1" />

        <Button Text="3"
                Command="{Binding AddCharCommand}"
                CommandParameter="3"
                Grid.Row="1" Grid.Column="2" />

        <Button Text="4"
                Command="{Binding AddCharCommand}"
                CommandParameter="4"
                Grid.Row="2" Grid.Column="0" />

        <Button Text="5"
                Command="{Binding AddCharCommand}"
                CommandParameter="5"
                Grid.Row="2" Grid.Column="1" />

        <Button Text="6"
                Command="{Binding AddCharCommand}"
                CommandParameter="6"
                Grid.Row="2" Grid.Column="2" />

        <Button Text="7"
                Command="{Binding AddCharCommand}"
                CommandParameter="7"
                Grid.Row="3" Grid.Column="0" />

        <Button Text="8"
                Command="{Binding AddCharCommand}"
                CommandParameter="8"
                Grid.Row="3" Grid.Column="1" />

        <Button Text="9"
                Command="{Binding AddCharCommand}"
                CommandParameter="9"
                Grid.Row="3" Grid.Column="2" />

        <Button Text="*"
                Command="{Binding AddCharCommand}"
                CommandParameter="*"
                Grid.Row="4" Grid.Column="0" />

        <Button Text="0"
                Command="{Binding AddCharCommand}"
                CommandParameter="0"
                Grid.Row="4" Grid.Column="1" />

        <Button Text="#"
                Command="{Binding AddCharCommand}"
                CommandParameter="#"
                Grid.Row="4" Grid.Column="2" />
    </Grid>
</ContentPage>
```

La `Command` propriété du premier `Button` qui apparaît dans ce balisage est liée à `DeleteCharCommand` ; le reste est lié au `AddCharCommand` avec un `CommandParameter` qui est le même que le caractère qui apparaît sur la `Button` face. Voici le programme en action :

[![](data-bindings-to-mvvm-images/keypad.png "Calculator using MVVM and Commands")](data-bindings-to-mvvm-images/keypad-large.png#lightbox "Calculator using MVVM and Commands")

### <a name="invoking-asynchronous-methods"></a>Appel de méthodes asynchrones

Les commandes peuvent également appeler des méthodes asynchrones. Pour ce faire, utilisez les `async` `await` Mots clés et lors de la spécification de la `Execute` méthode :

```csharp
DownloadCommand = new Command (async () => await DownloadAsync ());
```

Cela indique que la `DownloadAsync` méthode est un `Task` et doit être attendue :

```csharp
async Task DownloadAsync ()
{
    await Task.Run (() => Download ());
}

void Download ()
{
    ...
}
```

## <a name="implementing-a-navigation-menu"></a>Implémentation d’un menu de navigation

Le programme [XamlSamples](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples) qui contient tout le code source de cette série d’articles utilise un ViewModel pour sa page d’origine. Ce ViewModel est une définition d’une classe Short avec trois propriétés nommées `Type` , `Title` et `Description` qui contiennent le type de chacun des exemples de pages, un titre et une brève description. En outre, le ViewModel définit une propriété statique nommée `All` qui est une collection de toutes les pages du programme :

```csharp
public class PageDataViewModel
{
    public PageDataViewModel(Type type, string title, string description)
    {
        Type = type;
        Title = title;
        Description = description;
    }

    public Type Type { private set; get; }

    public string Title { private set; get; }

    public string Description { private set; get; }

    static PageDataViewModel()
    {
        All = new List<PageDataViewModel>
        {
            // Part 1. Getting Started with XAML
            new PageDataViewModel(typeof(HelloXamlPage), "Hello, XAML",
                                  "Display a Label with many properties set"),

            new PageDataViewModel(typeof(XamlPlusCodePage), "XAML + Code",
                                  "Interact with a Slider and Button"),

            // Part 2. Essential XAML Syntax
            new PageDataViewModel(typeof(GridDemoPage), "Grid Demo",
                                  "Explore XAML syntax with the Grid"),

            new PageDataViewModel(typeof(AbsoluteDemoPage), "Absolute Demo",
                                  "Explore XAML syntax with AbsoluteLayout"),

            // Part 3. XAML Markup Extensions
            new PageDataViewModel(typeof(SharedResourcesPage), "Shared Resources",
                                  "Using resource dictionaries to share resources"),

            new PageDataViewModel(typeof(StaticConstantsPage), "Static Constants",
                                  "Using the x:Static markup extensions"),

            new PageDataViewModel(typeof(RelativeLayoutPage), "Relative Layout",
                                  "Explore XAML markup extensions"),

            // Part 4. Data Binding Basics
            new PageDataViewModel(typeof(SliderBindingsPage), "Slider Bindings",
                                  "Bind properties of two views on the page"),

            new PageDataViewModel(typeof(SliderTransformsPage), "Slider Transforms",
                                  "Use Sliders with reverse bindings"),

            new PageDataViewModel(typeof(ListViewDemoPage), "ListView Demo",
                                  "Use a ListView with data bindings"),

            // Part 5. From Data Bindings to MVVM
            new PageDataViewModel(typeof(OneShotDateTimePage), "One-Shot DateTime",
                                  "Obtain the current DateTime and display it"),

            new PageDataViewModel(typeof(ClockPage), "Clock",
                                  "Dynamically display the current time"),

            new PageDataViewModel(typeof(HslColorScrollPage), "HSL Color Scroll",
                                  "Use a view model to select HSL colors"),

            new PageDataViewModel(typeof(KeypadPage), "Keypad",
                                  "Use a view model for numeric keypad logic")
        };
    }

    public static IList<PageDataViewModel> All { private set; get; }
}
```

Le fichier XAML pour `MainPage` définit un `ListBox` dont la `ItemsSource` propriété a la valeur de cette `All` propriété et qui contient un `TextCell` pour afficher les `Title` `Description` Propriétés et de chaque page :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples"
             x:Class="XamlSamples.MainPage"
             Padding="5, 0"
             Title="XAML Samples">

    <ListView ItemsSource="{x:Static local:PageDataViewModel.All}"
              ItemSelected="OnListViewItemSelected">
        <ListView.ItemTemplate>
            <DataTemplate>
                <TextCell Text="{Binding Title}"
                          Detail="{Binding Description}" />
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
</ContentPage>
```

Les pages sont affichées dans une liste déroulante :

[![](data-bindings-to-mvvm-images/mainpage.png "Scrollable list of pages")](data-bindings-to-mvvm-images/mainpage-large.png#lightbox "Scrollable list of pages")

Le gestionnaire du fichier code-behind est déclenché lorsque l’utilisateur sélectionne un élément. Le gestionnaire affecte `SelectedItem` à la propriété de la `ListBox` valeur `null` , puis instancie la page sélectionnée et y accède :

```csharp
private async void OnListViewItemSelected(object sender, SelectedItemChangedEventArgs args)
{
    (sender as ListView).SelectedItem = null;

    if (args.SelectedItem != null)
    {
        PageDataViewModel pageData = args.SelectedItem as PageDataViewModel;
        Page page = (Page)Activator.CreateInstance(pageData.Type);
        await Navigation.PushAsync(page);
    }
}
```

## <a name="video"></a>Vidéo

> [!VIDEO https://youtube.com/embed/DYRLcqG2BAY]

**Xamarin évoluer 2016 : MVVM simple avec Xamarin.Forms et Prism**

## <a name="summary"></a>Résumé

XAML est un outil puissant permettant de définir des interfaces utilisateur dans des Xamarin.Forms applications, notamment lorsque la liaison de données et MVVM sont utilisées. Le résultat est une représentation propre, élégante et pouvant être intégrée à une interface utilisateur avec toute la prise en charge en arrière-plan dans le code.

## <a name="related-links"></a>Liens connexes

- [XamlSamples](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)
- [Partie 1. Prise en main avec XAML](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [Partie 2. Syntaxe XAML essentielle](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [Partie 3. Extensions de balisage XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Partie 4. Principes fondamentaux de la liaison de données](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)

## <a name="related-videos"></a>Vidéos connexes

> [!Video https://channel9.msdn.com/Series/Xamarin-101/XamarinForms-MVVM-with-XAML-6-of-11/player]

> [!Video https://channel9.msdn.com/Series/Xamarin-101/XamarinForms-Navigation-with-XAML-7-of-11/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
