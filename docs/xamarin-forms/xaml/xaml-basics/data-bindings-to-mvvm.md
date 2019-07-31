---
title: Partie 5. Liaisons de données à MVVM
description: Le modèle MVVM applique une séparation entre les trois couches logicielles, l’interface utilisateur XAML, appelée la vue ; les données sous-jacentes, appelées le modèle ; et un intermédiaire entre la vue et le modèle, appelé le ViewModel.
ms.prod: xamarin
ms.assetid: 48B37D44-4FB1-41B2-9A5E-6D383B041F81
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/25/2017
ms.openlocfilehash: 11bca4bc74316f87ab7b329c897efcd4b768bc03
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68657109"
---
# <a name="part-5-from-data-bindings-to-mvvm"></a>Partie 5. Liaisons de données à MVVM

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)

_Le modèle d’architecture Model-View-ViewModel (MVVM) a été inventé avec XAML à l’esprit. Le modèle met en œuvre une séparation entre les trois couches logicielles, l’interface utilisateur XAML, appelée la vue ; les données sous-jacentes, appelées le modèle ; et un intermédiaire entre la vue et le modèle, appelé le ViewModel. La vue et le ViewModel sont souvent connectés via des liaisons de données définies dans le fichier XAML. BindingContext pour la vue est généralement une instance du ViewModel._

## <a name="a-simple-viewmodel"></a>Un ViewModel Simple

Une introduction aux ViewModels, commençons par examiner un programme sans un.
Vous avez vu précédemment comment définir une nouvelle déclaration d’espace de noms XML pour permettre à un fichier XAML pour les classes de référence dans d’autres assemblys. Voici un programme qui définit une déclaration d’espace de noms XML pour le `System` espace de noms :

```csharp
xmlns:sys="clr-namespace:System;assembly=mscorlib"
```

Le programme peut utiliser `x:Static` pour obtenir la date et heure actuelles à partir de la méthode statique `DateTime.Now` propriété et définissez-les `DateTime` valeur pour le `BindingContext` sur un `StackLayout`:

```xaml
<StackLayout BindingContext="{x:Static sys:DateTime.Now}" …>
```

`BindingContext`est une propriété très spéciale: Quand vous définissez la `BindingContext` sur un élément, elle est héritée par tous les enfants de cet élément. Cela signifie que tous les enfants de la `StackLayout` ont cette même `BindingContext`, et elles peuvent contenir des liaisons simples aux propriétés de cet objet.

Dans le **One-Shot DateTime** programme, deux des enfants contiennent des liaisons aux propriétés de le `DateTime` valeur, mais les deux autres enfants contiennent des liaisons qui semblent avoir disparu un chemin de liaison. Cela signifie que le `DateTime` valeur lui-même est utilisée pour le `StringFormat`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:sys="clr-namespace:System;assembly=mscorlib"
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

Bien entendu, le gros problème est que la date et l’heure sont ensemble une fois que lorsque la page est tout d’abord créée et ne changent jamais :

[![](data-bindings-to-mvvm-images/oneshotdatetime.png "Affichage comprenant la Date et l’heure")](data-bindings-to-mvvm-images/oneshotdatetime-large.png#lightbox "affichage comprenant la Date et l’heure")

Un fichier XAML peut afficher une horloge qui affiche toujours l’heure actuelle, mais il a besoin du code pour vous aider. Lorsque penser en termes de MVVM, le modèle et le ViewModel sont des classes écrites entièrement en code. La vue est souvent un fichier XAML qui référence des propriétés définies dans le ViewModel par le biais des liaisons de données.

Un modèle approprié est ignorant la du ViewModel et un ViewModel approprié est ignorant la de la vue. Toutefois, très souvent un programmeur personnalise les types de données exposés par le ViewModel pour les types de données associés à des interfaces utilisateur particulier. Par exemple, si un modèle accède à une base de données qui contient des chaînes ASCII de caractères 8 bits, le ViewModel devez effectuer une conversion entre ces chaînes en chaînes Unicode pour prendre en charge l’utilisation exclusive de caractères Unicode dans l’interface utilisateur.

Dans les exemples simples de MVVM (telles que celles présentées ici), souvent il n’existe aucun modèle du tout et le modèle implique simplement une vue et ViewModel lié avec des liaisons de données.

Voici un ViewModel pour une horloge avec une seule propriété nommée `DateTime`, mais les mises à jour qui `DateTime` propriété chaque seconde :

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

ViewModels implémentent généralement les `INotifyPropertyChanged` interface, ce qui signifie que la classe déclenche un `PropertyChanged` événement chaque fois qu’une de ses propriétés change. Le mécanisme de liaison de données dans Xamarin.Forms attache un gestionnaire à cet `PropertyChanged` événement et il peut être notifié lorsqu’une propriété change et conserver la cible mis à jour avec la nouvelle valeur.

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

Notez comment la `ClockViewModel` est défini sur le `BindingContext` de la `Label` à l’aide de balises d’élément de propriété. Ou bien, vous pouvez instancier la `ClockViewModel` dans un `Resources` collection et affectez-lui le `BindingContext` via un `StaticResource` extension de balisage. Ou bien, le fichier code-behind peut instancier le ViewModel.

Le `Binding` extension de balisage sur le `Text` propriété de la `Label` formats le `DateTime` propriété. Voici l’affichage :

[![](data-bindings-to-mvvm-images/clock.png "Affichage comprenant la Date et l’heure par le biais de ViewModel")](data-bindings-to-mvvm-images/clock-large.png#lightbox "affichage comprenant la Date et l’heure par le biais de ViewModel")

Il est également possible d’accéder aux propriétés individuelles de le `DateTime` propriété du ViewModel en les séparant par des points :

```xaml
<Label Text="{Binding DateTime.Second, StringFormat='{0}'}" … >
```

## <a name="interactive-mvvm"></a>MVVM interactive

MVVM est souvent utilisé avec des liaisons de données bidirectionnelle pour une vue interactive basée sur un modèle de données sous-jacent.

Voici une classe nommée `HslViewModel` qui convertit un `Color` valeur dans `Hue`, `Saturation`, et `Luminosity` valeurs et vice versa :

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

Passe à la `Hue`, `Saturation`, et `Luminosity` propriétés cause le `Color` propriété à modifier et les modifications apportées à `Color` entraîne les trois autres propriétés modifier. Cela peut sembler être une boucle infinie, à ceci près que la classe n’appeler le `PropertyChanged` événement, sauf si la propriété a changé. Cela met fin à la boucle de rétroaction sinon incontrôlable.

Le fichier XAML suivant contient un `BoxView` dont `Color` propriété est liée à la `Color` propriété de ViewModel et trois `Slider` et trois `Label` vues lié à la `Hue`, `Saturation`et `Luminosity` propriétés :

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

La liaison sur chaque `Label` est la valeur par défaut `OneWay`. Il doit uniquement afficher la valeur. Mais la liaison sur chaque `Slider` est `TwoWay`. Cela permet la `Slider` à initialiser à partir du ViewModel. Notez que le `Color` propriété est définie sur `Aqua` lorsque le ViewModel est instancié. Mais une modification dans le `Slider` doit également définir une nouvelle valeur pour la propriété dans le ViewModel, qui calcule ensuite une nouvelle couleur.

[![](data-bindings-to-mvvm-images/hslcolorscroll.png "MVVM à l’aide de liaisons de données bidirectionnel")](data-bindings-to-mvvm-images/hslcolorscroll-large.png#lightbox "MVVM à l’aide de liaisons de données bidirectionnel")

## <a name="commanding-with-viewmodels"></a>Exécution de commandes avec les ViewModels

Dans de nombreux cas, le modèle MVVM est limité à la manipulation d’éléments de données: Objets de l’interface utilisateur dans la vue Parallel Data Objects dans le ViewModel.

Toutefois, la vue doit parfois contenir des boutons qui déclenchent des actions différentes dans le ViewModel. Mais le ViewModel ne doit pas contenir `Clicked` gestionnaires pour les boutons, car qui serait lier le ViewModel à un modèle d’interface utilisateur particulier.

Qui permette aux ViewModels être plus indépendante des objets d’interface utilisateur particulier, mais autoriser les méthodes à appeler dans le ViewModel, un *commande* interface existe. Cette interface de commande est prise en charge par les éléments suivants dans Xamarin.Forms :

-  `Button`
-  `MenuItem`
-  `ToolbarItem`
-  `SearchBar`
-  `TextCell` (et donc également `ImageCell`)
-  `ListView`
-  `TapGestureRecognizer`

À l’exception de la `SearchBar` et `ListView` élément, ces éléments définissent deux propriétés :

-  `Command` de type  `System.Windows.Input.ICommand`
-  `CommandParameter` de type  `Object`

Le `SearchBar` définit `SearchCommand` et `SearchCommandParameter` propriétés, tandis que le `ListView` définit un `RefreshCommand` propriété de type `ICommand`.

Le `ICommand` interface définit deux méthodes et un événement :

-  `void Execute(object arg)`
-  `bool CanExecute(object arg)`
-  `event EventHandler CanExecuteChanged`

Le ViewModel peut définir des propriétés de type `ICommand`. Vous pouvez ensuite lier ces propriétés pour le `Command` propriété de chaque `Button` ou autre élément, ou peut-être une vue personnalisée qui implémente cette interface. Vous pouvez éventuellement définir le `CommandParameter` propriété pour identifier individuels `Button` objets (ou autres éléments) qui sont liés à cette propriété ViewModel. En interne, le `Button` appels le `Execute` méthode chaque fois que l’utilisateur appuie sur le `Button`, en passant à la `Execute` méthode son `CommandParameter`.

Le `CanExecute` (méthode) et `CanExecuteChanged` événements sont utilisés pour les cas où un `Button` tap est peut-être actuellement non valide, auquel cas la `Button` doit désactiver lui-même. Le `Button` appels `CanExecute` lorsque le `Command` propriété est d’abord définie et chaque fois que le `CanExecuteChanged` événement est déclenché. Si `CanExecute` retourne `false`, le `Button` désactive lui-même et ne génère pas de `Execute` appels.

Pour vous aider à ajouter des commandes à vos ViewModels, Xamarin.Forms définit deux classes qui implémentent `ICommand`: `Command` et `Command<T>` où `T` est le type des arguments de `Execute` et `CanExecute`. Ces deux classes définissent plusieurs constructeurs plus une `ChangeCanExecute` méthode que le ViewModel peut appeler pour forcer le `Command` objet pour déclencher le `CanExecuteChanged` événement.

Voici un ViewModel pour un pavé simple qui est prévu pour la saisie de numéros de téléphone. Notez que le `Execute` et `CanExecute` méthode sont définies comme lambda fonctions directement dans le constructeur :

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

Ce ViewModel suppose que le `AddCharCommand` propriété est liée à la `Command` propriété de plusieurs boutons (ou tout autre élément qui possède une interface de commande), chacun d’eux est identifiée par le `CommandParameter`. Ces boutons ajoutent des caractères à un `InputString` propriété, puis sous la forme d’un numéro de téléphone pour le `DisplayText` propriété.

Il existe également une deuxième propriété de type `ICommand` nommé `DeleteCharCommand`. Cela est lié à un bouton de l’espacement arrière, mais le bouton doit être désactivé si aucun caractère à supprimer.

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

Le `Command` propriété du premier `Button` qui s’affiche dans ce balisage est lié à la `DeleteCharCommand`; le reste sont liés à la `AddCharCommand` avec un `CommandParameter` qui est le même que le caractère qui apparaît sur le `Button` face. Voici le programme en action :

[![](data-bindings-to-mvvm-images/keypad.png "Calculatrice à l’aide des commandes et MVVM")](data-bindings-to-mvvm-images/keypad-large.png#lightbox "calculatrice à l’aide des commandes et MVVM")

### <a name="invoking-asynchronous-methods"></a>Appeler des méthodes asynchrones

Commandes peuvent également appeler des méthodes asynchrones. Pour cela, à l’aide de la `async` et `await` mots clés lorsque vous spécifiez le `Execute` (méthode) :

```csharp
DownloadCommand = new Command (async () => await DownloadAsync ());
```

Cela indique que le `DownloadAsync` méthode est un `Task` et doit être attendue :

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

Le [XamlSamples](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples) programme qui contient tout le code source dans cette série d’articles utilise un ViewModel pour sa page d’accueil. Ce ViewModel est une définition d’une classe courte avec trois propriétés nommées `Type`, `Title`, et `Description` qui contiennent le type de chacun des exemples de pages, un titre et une brève description. En outre, le ViewModel définit une propriété statique nommée `All` qui est une collection de toutes les pages dans le programme :

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

Le fichier XAML pour `MainPage` définit un `ListBox` dont `ItemsSource` propriété qui a `All` propriété et qui contient un `TextCell` pour afficher le `Title` et `Description` propriétés de chaque page :

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

[![](data-bindings-to-mvvm-images/mainpage.png "Liste déroulante des pages")](data-bindings-to-mvvm-images/mainpage-large.png#lightbox "liste déroulante des pages")

Le gestionnaire dans le fichier code-behind est déclenché lorsque l’utilisateur sélectionne un élément. Le gestionnaire affecte la `SelectedItem` propriété de la `ListBox` vers `null` puis instancie la page sélectionnée et navigue vers elle :

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

**Xamarin évoluer 2016: MVVM simplifié avec Xamarin. Forms et Prism**

## <a name="summary"></a>Récapitulatif

XAML est un outil puissant pour la définition des interfaces utilisateur dans les applications Xamarin.Forms, particulièrement lors de la liaison de données et MVVM sont utilisés. Le résultat est une représentation propre, élégante et potentiellement compatible avec les outils de l’interface utilisateur tout en arrière-plan prend en charge le code.


## <a name="related-links"></a>Liens associés

- [XamlSamples](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)
- [Partie 1. Bien démarrer avec XAML](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [Partie 2. Syntaxe XAML essentielle](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [Partie 3. Extensions de balisage XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Partie 4. Notions de base sur la liaison de données](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
