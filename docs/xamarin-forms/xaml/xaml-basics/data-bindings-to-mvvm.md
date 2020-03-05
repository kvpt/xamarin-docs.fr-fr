---
title: Partie 5. Des liaisons de données à MVVM
description: Le modèle MVVM applique une séparation entre les trois couches logicielles, l’interface utilisateur XAML, appelée la vue ; les données sous-jacentes, appelées le modèle ; et un intermédiaire entre la vue et le modèle, appelé le ViewModel.
ms.prod: xamarin
ms.custom: video
ms.assetid: 48B37D44-4FB1-41B2-9A5E-6D383B041F81
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/25/2017
ms.openlocfilehash: 1a6ab1393cbcd8224411aeea2af2aca27381bba3
ms.sourcegitcommit: 52fb214c0e0243587d4e9ad9306b75e92a8cc8b7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2020
ms.locfileid: "78292076"
---
# <a name="part-5-from-data-bindings-to-mvvm"></a>Partie 5. Des liaisons de données à MVVM

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)

_Le modèle d’architecture MVVM (Model-View-ViewModel) a été inventé avec XAML à l’esprit. Le modèle applique une séparation entre trois couches logicielles (l’interface utilisateur XAML, appelée vue). données sous-jacentes, appelées modèle ; et un intermédiaire entre la vue et le modèle, appelé ViewModel. La vue et le ViewModel sont souvent connectés via des liaisons de données définies dans le fichier XAML. L’BindingContext de la vue est généralement une instance du ViewModel._

## <a name="a-simple-viewmodel"></a>Un ViewModel Simple

Une introduction aux ViewModels, commençons par examiner un programme sans un.
Vous avez vu précédemment comment définir une nouvelle déclaration d’espace de noms XML pour permettre à un fichier XAML pour les classes de référence dans d’autres assemblys. Voici un programme qui définit une déclaration d’espace de noms XML pour l’espace de noms `System` :

```csharp
xmlns:sys="clr-namespace:System;assembly=netstandard"
```

Le programme peut utiliser `x:Static` pour obtenir la date et l’heure actuelles à partir de la propriété statique `DateTime.Now` et définir cette `DateTime` valeur sur la `BindingContext` sur un `StackLayout`:

```xaml
<StackLayout BindingContext="{x:Static sys:DateTime.Now}" …>
```

`BindingContext` est une propriété spéciale : quand vous définissez la `BindingContext` sur un élément, elle est héritée par tous les enfants de cet élément. Cela signifie que tous les enfants de la `StackLayout` ont ce même `BindingContext`et qu’ils peuvent contenir des liaisons simples aux propriétés de cet objet.

Dans le programme **DateTime à une seule capture** , deux des enfants contiennent des liaisons aux propriétés de cette `DateTime` valeur, mais deux autres enfants contiennent des liaisons qui semblent manquer un chemin de liaison. Cela signifie que la valeur de `DateTime` elle-même est utilisée pour la `StringFormat`:

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

Un fichier XAML peut afficher une horloge qui affiche toujours l’heure actuelle, mais il a besoin de code pour vous aider. Lorsque vous envisagez en termes de MVVM, le modèle et le ViewModel sont des classes écrites entièrement dans le code. La vue est souvent un fichier XAML qui référence des propriétés définies dans le ViewModel par le biais des liaisons de données.

Un modèle approprié est ignorant la du ViewModel et un ViewModel approprié est ignorant la de la vue. Toutefois, il arrive souvent qu’un programmeur personnalise les types de données exposés par le ViewModel aux types de données associés à des interfaces utilisateur particulières. Par exemple, si un modèle accède à une base de données qui contient des chaînes ASCII de caractères 8 bits, le ViewModel devez effectuer une conversion entre ces chaînes en chaînes Unicode pour prendre en charge l’utilisation exclusive de caractères Unicode dans l’interface utilisateur.

Dans les exemples simples de MVVM (telles que celles présentées ici), souvent il n’existe aucun modèle du tout et le modèle implique simplement une vue et ViewModel lié avec des liaisons de données.

Voici un ViewModel pour une horloge avec une seule propriété nommée `DateTime`, qui met à jour la propriété `DateTime` chaque seconde :

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

ViewModels implémente généralement l’interface `INotifyPropertyChanged`, ce qui signifie que la classe déclenche un événement `PropertyChanged` chaque fois qu’une de ses propriétés change. Le mécanisme de liaison de données dans Xamarin. Forms attache un gestionnaire à cet événement `PropertyChanged` afin qu’il puisse être averti lorsqu’une propriété change et que la cible est mise à jour avec la nouvelle valeur.

Une horloge selon ce ViewModel peut être aussi simple que cela :

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

Notez que la `ClockViewModel` est définie sur le `BindingContext` de la `Label` à l’aide de balises d’élément de propriété. Vous pouvez également instancier le `ClockViewModel` dans une collection `Resources` et le définir sur le `BindingContext` via une extension de balisage `StaticResource`. Ou bien, le fichier code-behind peut instancier le ViewModel.

L’extension de balisage `Binding` sur la propriété `Text` de l' `Label` met en forme la propriété `DateTime`. Voici l’affichage :

[![](data-bindings-to-mvvm-images/clock.png "View Displaying Date and Time via ViewModel")](data-bindings-to-mvvm-images/clock-large.png#lightbox "View Displaying Date and Time via ViewModel")

Il est également possible d’accéder aux propriétés individuelles de la propriété `DateTime` du ViewModel en séparant les propriétés par des points :

```xaml
<Label Text="{Binding DateTime.Second, StringFormat='{0}'}" … >
```

## <a name="interactive-mvvm"></a>MVVM interactive

MVVM est souvent utilisé avec des liaisons de données bidirectionnelles pour une vue interactive basée sur un modèle de données sous-jacent.

Voici une classe nommée `HslViewModel` qui convertit une valeur `Color` en valeurs `Hue`, `Saturation`et `Luminosity`, et vice versa :

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

Les modifications apportées aux propriétés `Hue`, `Saturation`et `Luminosity` entraînent la modification de la propriété `Color`, et les modifications apportées à `Color` entraînent la modification des trois autres propriétés. Cela peut ressembler à une boucle infinie, sauf que la classe n’appelle pas l’événement `PropertyChanged`, sauf si la propriété a changé. Cela met fin à la boucle de rétroaction sinon incontrôlable.

Le fichier XAML suivant contient un `BoxView` dont la propriété `Color` est liée à la propriété `Color` du ViewModel, et trois vues `Slider` et trois `Label` liées aux propriétés `Hue`, `Saturation`et `Luminosity` :

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

La liaison sur chaque `Label` est la `OneWay`par défaut. Il doit uniquement afficher la valeur. Mais la liaison sur chaque `Slider` est `TwoWay`. Cela permet d’initialiser le `Slider` à partir du ViewModel. Notez que la propriété `Color` a la valeur `Aqua` lorsque le ViewModel est instancié. Toutefois, une modification de la `Slider` doit également définir une nouvelle valeur pour la propriété dans le ViewModel, qui calcule ensuite une nouvelle couleur.

[![](data-bindings-to-mvvm-images/hslcolorscroll.png "MVVM using Two-Way Data Bindings")](data-bindings-to-mvvm-images/hslcolorscroll-large.png#lightbox "MVVM using Two-Way Data Bindings")

## <a name="commanding-with-viewmodels"></a>Exécution de commandes avec les ViewModels

Dans de nombreux cas, le modèle MVVM est limité à la manipulation d’éléments de données : les objets d’interface utilisateur dans la vue en parallèle des objets de données dans le ViewModel.

Toutefois, la vue doit parfois contenir des boutons qui déclenchent des actions différentes dans le ViewModel. Mais le ViewModel ne doit pas contenir de gestionnaires de `Clicked` pour les boutons, car cela lierait le ViewModel à un paradigme d’interface utilisateur particulier.

Pour permettre à ViewModels d’être plus indépendant d’objets d’interface utilisateur particuliers tout en autorisant l’appel de méthodes dans le ViewModel, il existe une interface de *commande* . Cette interface de commande est prise en charge par les éléments suivants dans Xamarin.Forms :

- `Button`
- `MenuItem`
- `ToolbarItem`
- `SearchBar`
- `TextCell` (et par conséquent également `ImageCell`)
- `ListView`
- `TapGestureRecognizer`

À l’exception de l’élément `SearchBar` et `ListView`, ces éléments définissent deux propriétés :

- `Command` de type `System.Windows.Input.ICommand`
- `CommandParameter` de type `Object`

Le `SearchBar` définit les propriétés `SearchCommand` et `SearchCommandParameter`, tandis que le `ListView` définit une propriété `RefreshCommand` de type `ICommand`.

L’interface `ICommand` définit deux méthodes et un événement :

- `void Execute(object arg)`
- `bool CanExecute(object arg)`
- `event EventHandler CanExecuteChanged`

Le ViewModel peut définir des propriétés de type `ICommand`. Vous pouvez ensuite lier ces propriétés à la propriété `Command` de chaque `Button` ou d’un autre élément, ou peut-être une vue personnalisée qui implémente cette interface. Vous pouvez éventuellement définir la propriété `CommandParameter` pour identifier les objets `Button` individuels (ou d’autres éléments) qui sont liés à cette propriété ViewModel. En interne, le `Button` appelle la méthode `Execute` chaque fois que l’utilisateur appuie sur le `Button`, en passant à la méthode `Execute` son `CommandParameter`.

La méthode `CanExecute` et `CanExecuteChanged` événement sont utilisés dans les cas où un TAP `Button` peut être actuellement non valide, auquel cas le `Button` doit se désactiver lui-même. Le `Button` appelle `CanExecute` lorsque la propriété `Command` est définie en premier et chaque fois que l’événement `CanExecuteChanged` est déclenché. Si `CanExecute` retourne `false`, le `Button` se désactive lui-même et ne génère pas d’appels `Execute`.

Pour obtenir de l’aide sur l’ajout de commandes à votre ViewModels, Xamarin. Forms définit deux classes qui implémentent `ICommand`: `Command` et `Command<T>` où `T` est le type des arguments à `Execute` et `CanExecute`. Ces deux classes définissent plusieurs constructeurs, ainsi qu’une méthode de `ChangeCanExecute` que le ViewModel peut appeler pour forcer l’objet `Command` à déclencher l’événement `CanExecuteChanged`.

Voici un ViewModel pour un pavé simple qui est prévu pour la saisie de numéros de téléphone. Notez que la méthode `Execute` et `CanExecute` sont définies comme fonctions lambda directement dans le constructeur :

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

Ce ViewModel suppose que la propriété `AddCharCommand` est liée à la propriété `Command` de plusieurs boutons (ou tout autre qui a une interface de commande), chacun d’eux étant identifié par le `CommandParameter`. Ces boutons ajoutent des caractères à une propriété de `InputString`, qui est ensuite mis en forme comme un numéro de téléphone pour la propriété `DisplayText`.

Il existe également une deuxième propriété de type `ICommand` nommée `DeleteCharCommand`. Cela est lié à un bouton de l’espacement arrière, mais le bouton doit être désactivé si aucun caractère à supprimer.

Le pavé suivant n’est pas sophistiqué comme visuellement qu’elle pourrait l’être. Au lieu de cela, le balisage a été réduit au minimum pour illustrer plus clairement l’utilisation de l’interface de commande :

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

La propriété `Command` du premier `Button` qui apparaît dans ce balisage est liée à la `DeleteCharCommand`; le reste est lié au `AddCharCommand` avec un `CommandParameter` identique au caractère qui apparaît sur la face `Button`. Voici le programme en action :

[![](data-bindings-to-mvvm-images/keypad.png "Calculator using MVVM and Commands")](data-bindings-to-mvvm-images/keypad-large.png#lightbox "Calculator using MVVM and Commands")

### <a name="invoking-asynchronous-methods"></a>Appeler des méthodes asynchrones

Commandes peuvent également appeler des méthodes asynchrones. Pour ce faire, utilisez les mots clés `async` et `await` lors de la spécification de la méthode `Execute` :

```csharp
DownloadCommand = new Command (async () => await DownloadAsync ());
```

Cela indique que la méthode `DownloadAsync` est une `Task` et doit être attendue :

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

## <a name="implementing-a-navigation-menu"></a>Implémentation d’un Menu de Navigation

Le programme [XamlSamples](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples) qui contient tout le code source de cette série d’articles utilise un ViewModel pour sa page d’origine. Ce ViewModel est une définition d’une classe Short avec trois propriétés nommées `Type`, `Title`et `Description` qui contiennent le type de chacun des exemples de pages, un titre et une brève description. En outre, le ViewModel définit une propriété statique nommée `All` qui est une collection de toutes les pages du programme :

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

Le fichier XAML pour `MainPage` définit un `ListBox` dont la propriété `ItemsSource` est définie sur cette `All` propriété et qui contient une `TextCell` pour afficher les propriétés `Title` et `Description` de chaque page :

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

Les pages sont affichés dans une liste déroulante :

[![](data-bindings-to-mvvm-images/mainpage.png "Scrollable list of pages")](data-bindings-to-mvvm-images/mainpage-large.png#lightbox "Scrollable list of pages")

Le gestionnaire dans le fichier code-behind est déclenché lorsque l’utilisateur sélectionne un élément. Le gestionnaire affecte à la propriété `SelectedItem` du `ListBox` la valeur `null` puis instancie la page sélectionnée et y accède :

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

**Xamarin évoluer 2016 : MVVM simplifié avec Xamarin. Forms et Prism**

## <a name="summary"></a>Résumé

XAML est un outil puissant pour la définition des interfaces utilisateur dans les applications Xamarin.Forms, particulièrement lors de la liaison de données et MVVM sont utilisés. Le résultat est une représentation propre, élégante et potentiellement compatible avec les outils de l’interface utilisateur tout en arrière-plan prend en charge le code.

## <a name="related-links"></a>Liens connexes

- [XamlSamples](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)
- [Partie 1. Prise en main avec XAML](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [Partie 2. Syntaxe XAML essentielle](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [Partie 3. Extensions de balisage XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Partie 4. Principes fondamentaux de la liaison de données](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)

## <a name="related-videos"></a>Vidéos associées

> [!Video https://channel9.msdn.com/Series/Xamarin-101/XamarinForms-MVVM-with-XAML-6-of-11/player]

> [!Video https://channel9.msdn.com/Series/Xamarin-101/XamarinForms-Navigation-with-XAML-7-of-11/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
