---
title: Xamarin.FormsInterface de commande
description: Cet article explique comment implémenter la propriété Command avec des Xamarin.Forms liaisons de données. L’interface d’exécution de commandes fournit une alternative à l’implémentation des commandes qui est beaucoup mieux adaptée à l’architecture MVVM.
ms.prod: xamarin
ms.assetid: 69922284-F398-45C3-B4CC-B8E29BB4C533
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 7edde81a926b142a5e792a203e96ee61b1fdfb7b
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562702"
---
# <a name="the-no-locxamarinforms-command-interface"></a>Xamarin.FormsInterface de commande

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)

Dans l’architecture MVVM (modèle-vue-vue modèle), les liaisons de données sont définies entre les propriétés de ViewModel (généralement une classe dérivée de `INotifyPropertyChanged`) et les propriétés dans la vue (généralement le fichier XAML). Parfois, une application a des besoins qui vont au-delà de ces liaisons de propriété en exigeant de l’utilisateur qu’il lance des commandes qui affectent un élément dans le ViewModel. Ces commandes sont généralement signalées par des clics de bouton ou des appuis tactiles et, en règle générale, elles sont traitées dans le fichier code-behind dans un gestionnaire pour l’événement `Clicked` du `Button` ou l’événement `Tapped` d’un `TapGestureRecognizer`.

L’interface d’exécution de commandes fournit une alternative à l’implémentation des commandes qui est beaucoup mieux adaptée à l’architecture MVVM. Le ViewModel lui-même peut contenir des commandes, qui sont des méthodes exécutées en réaction à une activité spécifique dans la vue, comme un clic de `Button`. Des liaisons de données sont définies entre ces commandes et le `Button`.

Pour permettre une liaison de données entre un `Button` et un ViewModel, le `Button` définit deux propriétés :

- [`Command`](xref:Xamarin.Forms.Button.Command) de type [`System.Windows.Input.ICommand`](xref:System.Windows.Input.ICommand)
- [`CommandParameter`](xref:Xamarin.Forms.Button.CommandParameter) de type `Object`

Pour utiliser l’interface de commande, vous définissez une liaison de données qui cible la propriété `Command` du `Button` où la source est une propriété dans le ViewModel de type `ICommand`. Le ViewModel contient du code associé à cette propriété `ICommand` qui est exécuté lors d’un clic sur le bouton. Vous pouvez définir `CommandParameter` en spécifiant des données arbitraires pour faire la distinction entre plusieurs boutons s’ils sont tous liés à la même propriété `ICommand` dans le ViewModel.

Les propriétés `Command` et `CommandParameter` sont également définies par les classes suivantes :

- [`MenuItem`](xref:Xamarin.Forms.MenuItem) et donc, [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) , qui dérive de `MenuItem`
- [`TextCell`](xref:Xamarin.Forms.TextCell) et donc, [`ImageCell`](xref:Xamarin.Forms.ImageCell) , qui dérive de `TextCell`
- [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer)

[`SearchBar`](xref:Xamarin.Forms.SearchBar) définit une [`SearchCommand`](xref:Xamarin.Forms.SearchBar.SearchCommand) propriété de type `ICommand` et une [`SearchCommandParameter`](xref:Xamarin.Forms.SearchBar.SearchCommandParameter) propriété. La [`RefreshCommand`](xref:Xamarin.Forms.ListView.RefreshCommand) propriété de [`ListView`](xref:Xamarin.Forms.ListView) est également de type `ICommand` .

Toutes ces commandes peuvent être gérées au sein d’un ViewModel d’une manière qui ne dépend pas de l’objet d’interface utilisateur particulier dans la vue.

## <a name="the-icommand-interface"></a>Interface ICommand

L' [`System.Windows.Input.ICommand`](xref:System.Windows.Input.ICommand) interface ne fait pas partie de Xamarin.Forms . Elle est définie dans l’espace de noms [System.Windows.Input](xref:System.Windows.Input) et se compose de deux méthodes et d’un événement :

```csharp
public interface ICommand
{
    public void Execute (Object parameter);

    public bool CanExecute (Object parameter);

    public event EventHandler CanExecuteChanged;
}
```

Pour utiliser l’interface de commande, votre ViewModel contient des propriétés de type `ICommand` :

```csharp
public ICommand MyCommand { private set; get; }
```

Le ViewModel doit également référencer une classe qui implémente l’interface `ICommand`. Cette classe sera décrite sous peu. Dans la vue, la propriété `Command` d’un `Button` est liée à cette propriété :

```xaml
<Button Text="Execute command"
        Command="{Binding MyCommand}" />
```

Lorsque l’utilisateur appuie sur le `Button`, le `Button` appelle la méthode `Execute` dans l’objet `ICommand` lié à sa propriété `Command`. C’est la partie la plus simple de l’interface d’exécution de commandes.

La méthode `CanExecute` est plus complexe. Lorsque la liaison est initialement définie sur la propriété `Command` du `Button`, quand la liaison de données est modifiée d’une façon ou d’une autre, le `Button` appelle la méthode `CanExecute` dans l’objet `ICommand`. Si `CanExecute` retourne `false`, le `Button` se désactive. Cela indique que la commande particulière est actuellement indisponible ou non valide.

Le `Button` attache également un gestionnaire sur l’événement `CanExecuteChanged` de l’objet `ICommand`. Cet événement est déclenché à partir du ViewModel. Lorsque cet événement est déclenché, le `Button` appelle `CanExecute` à nouveau. Le `Button` s’active lui-même si `CanExecute` retourne `true` et se désactive si `CanExecute` retourne `false`.

> [!IMPORTANT]
> N’utilisez pas la propriété `IsEnabled` de `Button` si vous utilisez l’interface de commande.  

## <a name="the-command-class"></a>Classe Command

Lorsque votre ViewModel définit une propriété de type `ICommand`, le ViewModel doit également contenir ou référencer une classe implémentant l’interface `ICommand`. Cette classe doit contenir ou référencer les méthodes `Execute` et `CanExecute`, et déclencher l’événement `CanExecuteChanged` chaque fois que la méthode `CanExecute` peut retourner une valeur différente.

Vous pouvez écrire une telle classe vous-même ou utiliser une classe écrite par quelqu'un d’autre. Comme `ICommand` fait partie de Microsoft Windows, cet objet a été utilisé pendant des années avec les applications MVVM de Windows. L’utilisation d’une classe Windows qui implémente `ICommand` vous permet de partager votre ViewModels entre les applications Windows et les Xamarin.Forms applications.

Si le partage de ViewModels entre Windows et Xamarin.Forms n’est pas un problème, vous pouvez utiliser la [`Command`](xref:Xamarin.Forms.Command) [`Command<T>`](xref:Xamarin.Forms.Command`1) classe ou incluse dans Xamarin.Forms pour implémenter l' `ICommand` interface. Ces classes vous permettent de spécifier les corps des méthodes `Execute` et `CanExecute` dans les constructeurs de classe. Utilisez `Command<T>` lorsque vous utilisez la propriété `CommandParameter` pour faire la distinction entre plusieurs vues liées à la même propriété `ICommand` et la classe `Command` plus simple, lorsque ce n’est pas exigé.

## <a name="basic-commanding"></a>Exécution de commandes de base

La page **Person Entry** (Saisie des détails de la personne) dans le programme [**Data Binding Demos**](/samples/xamarin/xamarin-forms-samples/databindingdemos) (Démos des liaisons de données) illustre des commandes simples, implémentées dans un ViewModel.

La classe `PersonViewModel` définit trois propriétés nommées `Name`, `Age` et `Skills` qui définissent une personne. Cette classe *ne contient pas* de propriété `ICommand` :

```csharp
public class PersonViewModel : INotifyPropertyChanged
{
    string name;
    double age;
    string skills;

    public event PropertyChangedEventHandler PropertyChanged;

    public string Name
    {
        set { SetProperty(ref name, value); }
        get { return name; }
    }

    public double Age
    {
        set { SetProperty(ref age, value); }
        get { return age; }
    }

    public string Skills
    {
        set { SetProperty(ref skills, value); }
        get { return skills; }
    }

    public override string ToString()
    {
        return Name + ", age " + Age;
    }

    bool SetProperty<T>(ref T storage, T value, [CallerMemberName] string propertyName = null)
    {
        if (Object.Equals(storage, value))
            return false;

        storage = value;
        OnPropertyChanged(propertyName);
        return true;
    }

    protected void OnPropertyChanged([CallerMemberName] string propertyName = null)
    {
        PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
    }
}
```

La classe `PersonCollectionViewModel` affichée ci-dessous crée de nouveaux objets de type `PersonViewModel` et permet à l’utilisateur de renseigner les données. Pour ce faire, la classe définit les propriétés `IsEditing` de type `bool` et `PersonEdit` de type `PersonViewModel`. En outre, la classe définit trois propriétés de type `ICommand` et une propriété nommée `Persons` de type `IList<PersonViewModel>` :

```csharp
public class PersonCollectionViewModel : INotifyPropertyChanged
{
    PersonViewModel personEdit;
    bool isEditing;

    public event PropertyChangedEventHandler PropertyChanged;

    ···

    public bool IsEditing
    {
        private set { SetProperty(ref isEditing, value); }
        get { return isEditing; }
    }

    public PersonViewModel PersonEdit
    {
        set { SetProperty(ref personEdit, value); }
        get { return personEdit; }
    }

    public ICommand NewCommand { private set; get; }

    public ICommand SubmitCommand { private set; get; }

    public ICommand CancelCommand { private set; get; }

    public IList<PersonViewModel> Persons { get; } = new ObservableCollection<PersonViewModel>();

    bool SetProperty<T>(ref T storage, T value, [CallerMemberName] string propertyName = null)
    {
        if (Object.Equals(storage, value))
            return false;

        storage = value;
        OnPropertyChanged(propertyName);
        return true;
    }

    protected void OnPropertyChanged([CallerMemberName] string propertyName = null)
    {
        PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
    }
}
```

Cette liste abrégée n’inclut pas le constructeur de la classe, à savoir l’emplacement où les trois propriétés de type `ICommand` sont définies, qui sera montré sous peu. Notez que les modifications apportées aux trois propriétés de type `ICommand` et à la propriété `Persons` n’entraînent pas le déclenchement des événements `PropertyChanged`. Ces propriétés sont toutes définies lors de la création initiale de la classe et ne changent pas par la suite.

Avant d’examiner le constructeur de la classe `PersonCollectionViewModel`, examinons le fichier XAML pour le programme **Person Entry** (Saisie d’informations personnelles). Ce fichier contient un objet `Grid` avec sa propriété `BindingContext` définie sur `PersonCollectionViewModel`. L’objet `Grid` contient un `Button` avec le texte **New** (Nouveau) avec sa propriété `Command` liée à la propriété `NewCommand` dans le ViewModel, un formulaire d’entrée avec des propriétés liées à la propriété `IsEditing`, ainsi que les propriétés de `PersonViewModel`, et deux boutons supplémentaires liés aux propriétés `SubmitCommand` et `CancelCommand` du ViewModel. Le `ListView` final affiche la collection des personnes déjà saisies :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.PersonEntryPage"
             Title="Person Entry">
    <Grid Margin="10">
        <Grid.BindingContext>
            <local:PersonCollectionViewModel />
        </Grid.BindingContext>

        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <!-- New Button -->
        <Button Text="New"
                Grid.Row="0"
                Command="{Binding NewCommand}"
                HorizontalOptions="Start" />

        <!-- Entry Form -->
        <Grid Grid.Row="1"
              IsEnabled="{Binding IsEditing}">

            <Grid BindingContext="{Binding PersonEdit}">
                <Grid.RowDefinitions>
                    <RowDefinition Height="Auto" />
                    <RowDefinition Height="Auto" />
                    <RowDefinition Height="Auto" />
                </Grid.RowDefinitions>

                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="Auto" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>

                <Label Text="Name: " Grid.Row="0" Grid.Column="0" />
                <Entry Text="{Binding Name}"
                       Grid.Row="0" Grid.Column="1" />

                <Label Text="Age: " Grid.Row="1" Grid.Column="0" />
                <StackLayout Orientation="Horizontal"
                             Grid.Row="1" Grid.Column="1">
                    <Stepper Value="{Binding Age}"
                             Maximum="100" />
                    <Label Text="{Binding Age, StringFormat='{0} years old'}"
                           VerticalOptions="Center" />
                </StackLayout>

                <Label Text="Skills: " Grid.Row="2" Grid.Column="0" />
                <Entry Text="{Binding Skills}"
                       Grid.Row="2" Grid.Column="1" />

            </Grid>
        </Grid>

        <!-- Submit and Cancel Buttons -->
        <Grid Grid.Row="2">
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="*" />
                <ColumnDefinition Width="*" />
            </Grid.ColumnDefinitions>

            <Button Text="Submit"
                    Grid.Column="0"
                    Command="{Binding SubmitCommand}"
                    VerticalOptions="CenterAndExpand" />

            <Button Text="Cancel"
                    Grid.Column="1"
                    Command="{Binding CancelCommand}"
                    VerticalOptions="CenterAndExpand" />
        </Grid>

        <!-- List of Persons -->
        <ListView Grid.Row="3"
                  ItemsSource="{Binding Persons}" />
    </Grid>
</ContentPage>
```

Voici comment cela fonctionne : l’utilisateur appuie initialement sur le bouton **New** (Nouveau). Cela active le formulaire d’entrée mais désactive le bouton **New**. L’utilisateur entre alors un nom, un âge et des compétences. À tout moment pendant la saisie, l’utilisateur peut appuyer sur le bouton **Annuler** pour repartir de zéro. Une fois seulement qu’un nom et un âge valides ont été entrés, le bouton **Submit** (Envoyer) est activé. L’appui sur le bouton **Submit** transfère la personne vers la collection affichée par le `ListView`. Après un appui sur le bouton **Cancel** (Annuler) ou **Submit** (Envoyer), le formulaire d’entrée est effacé et le bouton **New** (Nouveau) est activé de nouveau.

L’écran iOS de gauche montre la disposition avant qu’un âge valide soit entré. L’écran Android affiche le bouton **Envoyer** activé après la définition d’une ancienneté :

[![Entrée de personne](commanding-images/personentry-small.png "Entrée de personne")](commanding-images/personentry-large.png#lightbox "Entrée de personne")

Le programme n’a aucune fonctionnalité pour modifier des entrées existantes et n’enregistre pas les entrées lorsque vous quittez la page.

Toute la logique pour les boutons **New**, **Submit** et **Cancel** est gérée dans `PersonCollectionViewModel` via les définitions des propriétés `NewCommand`, `SubmitCommand` et `CancelCommand`. Le constructeur du `PersonCollectionViewModel` définit ces trois propriétés sur des objets de type `Command`.  

Un [constructeur](xref:Xamarin.Forms.Command.%23ctor(System.Action,System.Func{System.Boolean})) de la classe `Command` vous permet de passer des arguments de type `Action` et `Func<bool>` correspondant aux méthodes `Execute` et `CanExecute`. Le plus facile est de définir ces actions et fonctions en tant que fonctions lambda directement dans le constructeur `Command`. Voici la définition de l’objet `Command` pour la propriété `NewCommand` :

```csharp
public class PersonCollectionViewModel : INotifyPropertyChanged
{

    ···

    public PersonCollectionViewModel()
    {
        NewCommand = new Command(
            execute: () =>
            {
                PersonEdit = new PersonViewModel();
                PersonEdit.PropertyChanged += OnPersonEditPropertyChanged;
                IsEditing = true;
                RefreshCanExecutes();
            },
            canExecute: () =>
            {
                return !IsEditing;
            });

        ···

    }

    void OnPersonEditPropertyChanged(object sender, PropertyChangedEventArgs args)
    {
        (SubmitCommand as Command).ChangeCanExecute();
    }

    void RefreshCanExecutes()
    {
        (NewCommand as Command).ChangeCanExecute();
        (SubmitCommand as Command).ChangeCanExecute();
        (CancelCommand as Command).ChangeCanExecute();
    }

    ···

}
```

Lorsque l’utilisateur clique sur le bouton **New**, la fonction `execute` passée au constructeur `Command` est exécutée. Ceci crée un nouvel objet `PersonViewModel`, définit un gestionnaire sur l’événement `PropertyChanged` de cet objet, définit `IsEditing` sur `true`et appelle la méthode `RefreshCanExecutes` définie après le constructeur.

Outre l’implémentation de l’interface `ICommand`, la classe `Command` définit également une méthode nommée `ChangeCanExecute`. Votre ViewModel doit appeler `ChangeCanExecute` pour une propriété `ICommand` chaque fois que quelque chose se produit qui peut changer la valeur renvoyée de la méthode `CanExecute`. Un appel à `ChangeCanExecute` provoque le déclenchement de la méthode `CanExecuteChanged` par la classe `Command`. Le `Button` dispose d’un gestionnaire joint pour cet événement et répond en appelant `CanExecute` à nouveau, puis en s’activant lui-même sur la base de la valeur renvoyée par cette méthode.

Lorsque la méthode `execute` de `NewCommand` appelle `RefreshCanExecutes`, la propriété `NewCommand` reçoit un appel à `ChangeCanExecute` et le `Button` appelle la méthode `canExecute`, qui retourne maintenant `false`, car la propriété `IsEditing` est désormais égale à `true`.

Le gestionnaire `PropertyChanged` pour le nouvel objet `PersonViewModel` appelle la méthode `ChangeCanExecute` de `SubmitCommand`. Voici comment cette propriété Command est implémentée :

```csharp
public class PersonCollectionViewModel : INotifyPropertyChanged
{

    ···

    public PersonCollectionViewModel()
    {

        ···

        SubmitCommand = new Command(
            execute: () =>
            {
                Persons.Add(PersonEdit);
                PersonEdit.PropertyChanged -= OnPersonEditPropertyChanged;
                PersonEdit = null;
                IsEditing = false;
                RefreshCanExecutes();
            },
            canExecute: () =>
            {
                return PersonEdit != null &&
                       PersonEdit.Name != null &&
                       PersonEdit.Name.Length > 1 &&
                       PersonEdit.Age > 0;
            });

        ···
    }

    ···

}
```

La fonction `canExecute` pour `SubmitCommand` est appelée chaque fois qu’il existe une propriété modifiée dans l’objet `PersonViewModel` en cours de modification. Elle retourne `true` uniquement lorsque la propriété `Name` contient au moins un caractère et que la propriété `Age` est supérieure à 0. À ce stade, le bouton **Submit** devient actif.

La fonction `execute` pour **Submit** supprime le gestionnaire de modification de propriété à partir du `PersonViewModel`, ajoute l’objet à la collection `Persons` et renvoie tout aux conditions initiales.

La fonction `execute` pour le bouton **Cancel** fait tout ce que le bouton **Submit** fait, si ce n’est ajouter l’objet à la collection :

```csharp
public class PersonCollectionViewModel : INotifyPropertyChanged
{

    ···

    public PersonCollectionViewModel()
    {

        ···

        CancelCommand = new Command(
            execute: () =>
            {
                PersonEdit.PropertyChanged -= OnPersonEditPropertyChanged;
                PersonEdit = null;
                IsEditing = false;
                RefreshCanExecutes();
            },
            canExecute: () =>
            {
                return IsEditing;
            });
    }

    ···

}
```

La méthode `canExecute` retourne `true` lorsqu’un `PersonViewModel` est en cours de modification.

Ces techniques pourraient être adaptées à des scénarios plus complexes : une propriété dans `PersonCollectionViewModel` pourrait être liée à la propriété `SelectedItem` du `ListView` pour modifier des éléments existants et un bouton **Delete** pourrait être ajouté pour supprimer ces éléments.

Il n’est pas nécessaire de définir les méthodes `execute` et `canExecute` en tant que fonctions lambda. Vous pouvez les écrire en tant que méthodes privées standard dans le ViewModel et les référencer dans les constructeurs `Command`. Toutefois, cette approche a tendance à générer un grand nombre de méthodes qui sont référencées une seule fois dans le ViewModel.

## <a name="using-command-parameters"></a>Utilisation des paramètres de commande

Il est parfois pratique de partager la même propriété `ICommand` dans le ViewModel pour un ou plusieurs boutons (ou d’autres objets d’interface utilisateur). Dans ce cas, vous utilisez la propriété `CommandParameter` pour faire la distinction entre les boutons.

Vous pouvez continuer à utiliser la classe `Command` pour ces propriétés `ICommand` partagées. La classe définit un [constructeur alternatif](xref:Xamarin.Forms.Command.%23ctor(System.Action{System.Object},System.Func{System.Object,System.Boolean})) qui accepte les méthodes `execute` et `canExecute` avec des paramètres de type `Object`. C’est ainsi que `CommandParameter` est passé à ces méthodes.

Toutefois, lorsque `CommandParameter` vous utilisez, il est plus facile d’utiliser la [`Command<T>`](xref:Xamarin.Forms.Command`1) classe générique pour spécifier le type de l’objet défini sur `CommandParameter` . Les méthodes `execute` et `canExecute` que vous spécifiez ont des paramètres de ce type.

La page **Decimal Keyboard** (Clavier décimal) illustre cette technique en montrant comment implémenter un pavé numérique pour la saisie de nombres décimaux. Le `BindingContext` pour l’objet `Grid` est un `DecimalKeypadViewModel`. La propriété `Entry` de ce ViewModel est liée à la propriété `Text` d’un objet `Label`. Tous les objets `Button` sont liés à diverses commandes dans le ViewModel : `ClearCommand`, `BackspaceCommand` et `DigitCommand` :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.DecimalKeypadPage"
             Title="Decimal Keyboard">

    <Grid WidthRequest="240"
          HeightRequest="480"
          ColumnSpacing="2"
          RowSpacing="2"
          HorizontalOptions="Center"
          VerticalOptions="Center">

        <Grid.BindingContext>
            <local:DecimalKeypadViewModel />
        </Grid.BindingContext>

        <Grid.Resources>
            <ResourceDictionary>
                <Style TargetType="Button">
                    <Setter Property="FontSize" Value="32" />
                    <Setter Property="BorderWidth" Value="1" />
                    <Setter Property="BorderColor" Value="Black" />
                </Style>
            </ResourceDictionary>
        </Grid.Resources>

        <Label Text="{Binding Entry}"
               Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="3"
               FontSize="32"
               LineBreakMode="HeadTruncation"
               VerticalTextAlignment="Center"
               HorizontalTextAlignment="End" />

        <Button Text="CLEAR"
                Grid.Row="1" Grid.Column="0" Grid.ColumnSpan="2"
                Command="{Binding ClearCommand}" />

        <Button Text="&#x21E6;"
                Grid.Row="1" Grid.Column="2"
                Command="{Binding BackspaceCommand}" />

        <Button Text="7"
                Grid.Row="2" Grid.Column="0"
                Command="{Binding DigitCommand}"
                CommandParameter="7" />

        <Button Text="8"
                Grid.Row="2" Grid.Column="1"
                Command="{Binding DigitCommand}"
                CommandParameter="8" />

        <Button Text="9"
                Grid.Row="2" Grid.Column="2"
                Command="{Binding DigitCommand}"
                CommandParameter="9" />

        <Button Text="4"
                Grid.Row="3" Grid.Column="0"
                Command="{Binding DigitCommand}"
                CommandParameter="4" />

        <Button Text="5"
                Grid.Row="3" Grid.Column="1"
                Command="{Binding DigitCommand}"
                CommandParameter="5" />

        <Button Text="6"
                Grid.Row="3" Grid.Column="2"
                Command="{Binding DigitCommand}"
                CommandParameter="6" />

        <Button Text="1"
                Grid.Row="4" Grid.Column="0"
                Command="{Binding DigitCommand}"
                CommandParameter="1" />

        <Button Text="2"
                Grid.Row="4" Grid.Column="1"
                Command="{Binding DigitCommand}"
                CommandParameter="2" />

        <Button Text="3"
                Grid.Row="4" Grid.Column="2"
                Command="{Binding DigitCommand}"
                CommandParameter="3" />

        <Button Text="0"
                Grid.Row="5" Grid.Column="0" Grid.ColumnSpan="2"
                Command="{Binding DigitCommand}"
                CommandParameter="0" />

        <Button Text="&#x00B7;"
                Grid.Row="5" Grid.Column="2"
                Command="{Binding DigitCommand}"
                CommandParameter="." />
    </Grid>
</ContentPage>
```

Les 11 boutons pour les 10 chiffres et la virgule décimale partagent une liaison à `DigitCommand`. La propriété `CommandParameter` fait la distinction entre ces boutons. La valeur définie sur `CommandParameter` est généralement la même que le texte affiché par le bouton à l’exception de la virgule décimale, qui, pour plus de clarté, est affichée sous la forme d’un point médian.

Voici le programme en action :

[![Clavier décimal](commanding-images/decimalkeyboard-small.png "Clavier décimal")](commanding-images/decimalkeyboard-large.png#lightbox "Clavier décimal")

Notez que le bouton de la virgule décimale dans les trois captures d’écran est désactivé, car le nombre saisi contient déjà une virgule décimale.

Le `DecimalKeypadViewModel` définit une propriété `Entry` de type `string` (qui est la seule propriété qui déclenche un événement `PropertyChanged`) et trois propriétés de type `ICommand` :

```csharp
public class DecimalKeypadViewModel : INotifyPropertyChanged
{
    string entry = "0";

    public event PropertyChangedEventHandler PropertyChanged;

    ···

    public string Entry
    {
        private set
        {
            if (entry != value)
            {
                entry = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Entry"));
            }
        }
        get
        {
            return entry;
        }
    }

    public ICommand ClearCommand { private set; get; }

    public ICommand BackspaceCommand { private set; get; }

    public ICommand DigitCommand { private set; get; }
}
```

Le bouton correspondant à la commande `ClearCommand` est toujours actif et réinitialise simplement l’entrée sur « 0 » :

```csharp
public class DecimalKeypadViewModel : INotifyPropertyChanged
{

    ···

    public DecimalKeypadViewModel()
    {
        ClearCommand = new Command(
            execute: () =>
            {
                Entry = "0";
                RefreshCanExecutes();
            });

        ···

    }

    void RefreshCanExecutes()
    {
        ((Command)BackspaceCommand).ChangeCanExecute();
        ((Command)DigitCommand).ChangeCanExecute();
    }

    ···

}
```

Comme le bouton est toujours actif, il n’est pas nécessaire de spécifier un argument `canExecute` dans le constructeur `Command`.

La logique de saisie des chiffres et du retour arrière est délicate car si aucun chiffre n’a été entré, la propriété `Entry` est la chaîne « 0 ». Si l’utilisateur saisit plusieurs zéros, la propriété `Entry` contient toujours un seul zéro. Si l’utilisateur saisit un autre chiffre, ce chiffre remplace le zéro. En revanche, si l’utilisateur saisit une virgule décimale avant tout autre chiffre, `Entry` est la chaîne « 0. ».

Le bouton **Backspace** (Retour arrière) est actif uniquement lorsque la longueur de l’entrée est supérieure à 1, ou si `Entry` n’est pas égal à la chaîne « 0 » :

```csharp
public class DecimalKeypadViewModel : INotifyPropertyChanged
{

    ···

    public DecimalKeypadViewModel()
    {

        ···

        BackspaceCommand = new Command(
            execute: () =>
            {
                Entry = Entry.Substring(0, Entry.Length - 1);
                if (Entry == "")
                {
                    Entry = "0";
                }
                RefreshCanExecutes();
            },
            canExecute: () =>
            {
                return Entry.Length > 1 || Entry != "0";
            });

        ···

    }

    ···

}
```

La logique de la fonction `execute` pour le bouton **Backspace** garantit que `Entry` est au moins une chaîne de « 0 ».

La propriété `DigitCommand` est liée à 11 boutons, dont chacun s’identifie à la propriété `CommandParameter`. La propriété `DigitCommand` peut être définie sur une instance de la classe `Command` standard, mais il est plus facile d’utiliser la classe générique `Command<T>`. Lorsque vous utilisez l’interface d’exécution de commandes avec XAML, les propriétés `CommandParameter` sont généralement des chaînes, et c’est le type de l’argument générique. Les fonctions `execute` et `canExecute` ont alors des arguments de type `string` :

```csharp
public class DecimalKeypadViewModel : INotifyPropertyChanged
{

    ···

    public DecimalKeypadViewModel()
    {

        ···

        DigitCommand = new Command<string>(
            execute: (string arg) =>
            {
                Entry += arg;
                if (Entry.StartsWith("0") && !Entry.StartsWith("0."))
                {
                    Entry = Entry.Substring(1);
                }
                RefreshCanExecutes();
            },
            canExecute: (string arg) =>
            {
                return !(arg == "." && Entry.Contains("."));
            });
    }

    ···

}
```

La méthode `execute` ajoute l’argument de chaîne à la propriété `Entry`. Toutefois, si le résultat commence par un zéro (mais pas par un zéro et une virgule décimale), ce zéro initial doit être supprimé à l’aide de la fonction `Substring`.

La méthode `canExecute` retourne `false` uniquement si l’argument est la virgule décimale (indiquant un appui sur la virgule décimale) et que `Entry` contient déjà une virgule décimale.

Toutes les méthodes `execute` appellent `RefreshCanExecutes`, qui appelle à son tour `ChangeCanExecute` pour `DigitCommand` et `ClearCommand`. Cela garantit que les boutons de virgule décimale et retour arrière sont actifs ou désactivés en fonction de la séquence actuelle de chiffres saisis.

## <a name="asynchronous-commanding-for-navigation-menus"></a>Exécution asynchrone de commandes pour les menus de navigation

L’exécution de commandes est pratique pour implémenter des menus de navigation, tels que celui du programme [**Data Binding Demos**](/samples/xamarin/xamarin-forms-samples/databindingdemos) (Démos des liaisons de données) lui-même. Voici une partie de **MainPage.xaml** :

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.MainPage"
             Title="Data Binding Demos"
             Padding="10">
    <TableView Intent="Menu">
        <TableRoot>
            <TableSection Title="Basic Bindings">

                <TextCell Text="Basic Code Binding"
                          Detail="Define a data-binding in code"
                          Command="{Binding NavigateCommand}"
                          CommandParameter="{x:Type local:BasicCodeBindingPage}" />

                <TextCell Text="Basic XAML Binding"
                          Detail="Define a data-binding in XAML"
                          Command="{Binding NavigateCommand}"
                          CommandParameter="{x:Type local:BasicXamlBindingPage}" />

                <TextCell Text="Alternative Code Binding"
                          Detail="Define a data-binding in code without a BindingContext"
                          Command="{Binding NavigateCommand}"
                          CommandParameter="{x:Type local:AlternativeCodeBindingPage}" />

                ···

            </TableSection>
        </TableRoot>
    </TableView>
</ContentPage>
```

Lors de l’utilisation de l’exécution de commandes avec XAML, les propriétés `CommandParameter` sont généralement définies en tant que chaînes. Dans ce cas, toutefois, une extension de balisage XAML est utilisée pour que le `CommandParameter` soit de type `System.Type`.

Chaque propriété `Command` est liée à une propriété nommée `NavigateCommand`. Cette propriété est définie dans le fichier code-behind, **MainPage.xaml.cs** :

```csharp
public partial class MainPage : ContentPage
{
    public MainPage()
    {
        InitializeComponent();

        NavigateCommand = new Command<Type>(
            async (Type pageType) =>
            {
                Page page = (Page)Activator.CreateInstance(pageType);
                await Navigation.PushAsync(page);
            });

        BindingContext = this;
    }

    public ICommand NavigateCommand { private set; get; }
}
```

Le constructeur définit la propriété `NavigateCommand` sur une méthode `execute` qui instancie le paramètre `System.Type`, puis accède à ce dernier. Étant donné que l’appel `PushAsync` nécessite un opérateur `await`, la méthode `execute` doit être marquée comme asynchrone. Cela s’effectue en plaçant le mot clé `async` avant la liste des paramètres.

Le constructeur définit également le `BindingContext` de la page sur lui-même afin que les liaisons référencent la propriété `NavigateCommand` dans cette classe.

L’ordre du code dans ce constructeur fait une différence : l’appel de `InitializeComponent` entraîne l’analyse du code XAML, mais à ce stade, la liaison à une propriété nommée `NavigateCommand` ne peut pas être résolue, car `BindingContext` est défini sur `null`. Si le `BindingContext` est défini dans le constructeur *avant que la propriété * `NavigateCommand` soit définie, la liaison peut être résolue lorsque `BindingContext` est défini, mais à ce stade, `NavigateCommand` a encore pour valeur `null`. La définition de `NavigateCommand` après `BindingContext` n’a aucun effet sur la liaison, car une modification de `NavigateCommand` ne déclenche pas d’événement `PropertyChanged`, et la liaison ne sait pas que `NavigateCommand` est désormais valide.

La définition des deux propriétés `NavigateCommand` et `BindingContext` (dans n’importe quel ordre) avant l’appel à `InitializeComponent` fonctionne, car les deux composantes de la liaison sont définies lorsque l’analyseur XAML rencontre la définition de la liaison.

Comme vous l’avez vu dans cette série d’articles, bien que les liaisons de données s’avèrent parfois délicates, elles sont puissantes et polyvalentes et offrent une aide considérable pour organiser le code en séparant la logique sous-jacente de l’interface utilisateur.

## <a name="related-links"></a>Liens associés

- [Démonstrations de liaison de données (exemple)](/samples/xamarin/xamarin-forms-samples/databindingdemos)
- [Chapitre sur la liaison de données à partir de Xamarin.Forms Book](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter18.md)