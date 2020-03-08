---
title: Mode de liaison Xamarin.Forms
description: Cet article explique comment contrôler le flux d’informations entre la source et la cible à l’aide d’un mode de liaison, qui est spécifié avec un membre de l’énumération BindingMode. Chaque propriété pouvant être liée possède un mode de liaison par défaut, lequel indique le mode en vigueur lorsque cette propriété est une cible de liaison de données.
ms.prod: xamarin
ms.assetid: D087C389-2E9E-47B9-A341-5B14AC732C45
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/01/2018
ms.openlocfilehash: 3bf1ab647faa4b6c4735585ddfeaeb704d7d3f41
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2020
ms.locfileid: "78911241"
---
# <a name="xamarinforms-binding-mode"></a>Mode de liaison Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)

Dans l’[article précédent](basic-bindings.md), les pages **Alternative Code Binding** (Liaison de code alternative) et **Alternative XAML Binding** (Liaison XAML alternative) présentaient un objet `Label` avec sa propriété `Scale` liée à la propriété `Value` d’un élément `Slider`. Comme la valeur initiale `Slider` est 0, la propriété `Scale` du `Label` était définie sur 0 au lieu de 1 et le `Label` disparaissait.

Dans l’exemple [**DataBindingDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos), la page **Reverse Binding** (Liaison inverse) est similaire aux programmes de l’article précédent, si ce n’est que la liaison de données est définie sur le `Slider` plutôt que sur le `Label`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DataBindingDemos.ReverseBindingPage"
             Title="Reverse Binding">
    <StackLayout Padding="10, 0">

        <Label x:Name="label"
               Text="TEXT"
               FontSize="80"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <Slider x:Name="slider"
                VerticalOptions="CenterAndExpand"
                Value="{Binding Source={x:Reference label},
                                Path=Opacity}" />
    </StackLayout>
</ContentPage>
```

Dans un premier temps, cela peut sembler inversé : désormais, le `Label` est la source de la liaison de données et le `Slider` en est la cible. La liaison référence la propriété `Opacity` du `Label`, qui a une valeur par défaut de 1.

Comme vous pouvez l’imaginer, le `Slider` est initialisé à la valeur 1 à partir de la valeur `Opacity` initiale du `Label`. Ceci est illustré dans la capture d’écran iOS de gauche :

[![Liaison inverse](binding-mode-images/reversebinding-small.png "Liaison inverse")](binding-mode-images/reversebinding-large.png#lightbox "Liaison inverse")

Mais vous serez peut-être surpris que le `Slider` continue de fonctionner, comme le montre la capture d’écran Android. Cela semble suggérer que la liaison de données fonctionne mieux lorsque le `Slider` est la cible de la liaison à la place du `Label`, car l’initialisation fonctionne comme on pouvait s’y attendre.

La différence entre l’exemple **Reverse Binding** (Liaison inverse) et les exemples précédents concerne le *mode de liaison*.

## <a name="the-default-binding-mode"></a>Mode de liaison par défaut

Le mode de liaison est spécifié avec un membre de l’énumération [`BindingMode`](xref:Xamarin.Forms.BindingMode) :

- [`Default`](xref:Xamarin.Forms.BindingMode.Default)
- [`TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) &ndash; données passent à la fois entre la source et la cible
- [`OneWay`](xref:Xamarin.Forms.BindingMode.OneWay) &ndash; données vont de la source à la cible
- [`OneWayToSource`](xref:Xamarin.Forms.BindingMode.OneWayToSource) &ndash; données passe de la cible à la source
- [`OneTime`](xref:Xamarin.Forms.BindingMode.OneWayToSource) &ndash; données vont de la source à la cible, mais uniquement lorsque le `BindingContext` change (nouveauté avec Xamarin. forms 3,0)

Chaque propriété pouvant être liée a un mode de liaison par défaut qui est défini lorsque la propriété pouvant être liée est créée, et qui est disponible à partir de la propriété [`DefaultBindingMode`](xref:Xamarin.Forms.BindableProperty.DefaultBindingMode) de l’objet `BindableProperty`. Ce mode de liaison par défaut indique le mode en vigueur lorsque cette propriété est une cible de liaison de données.

Le mode de liaison par défaut pour la plupart des propriétés, telles que `Rotation`, `Scale` et `Opacity`, est `OneWay`. Lorsque ces propriétés sont des cibles de liaison de données, la propriété cible est définie à partir de la source.

Toutefois, le mode de liaison par défaut pour la propriété `Value` du `Slider` est `TwoWay`. Cela signifie que lorsque la propriété `Value` est une cible de liaison de données, la cible est définie à partir de la source (comme d’habitude) mais la source est également définie à partir de la cible. C’est ce qui permet de définir le `Slider` à partir de la valeur `Opacity` initiale.

Cette liaison bidirectionnelle peut sembler créer une boucle infinie, mais ce n’est pas le cas. Les propriétés pouvant être liées ne signalent pas une modification de propriété, sauf si la propriété change véritablement. Cela empêche une boucle infinie.

### <a name="two-way-bindings"></a>Liaisons bidirectionnelles

La plupart des propriétés pouvant être liées ont le mode de liaison par défaut `OneWay`, mais les propriétés suivantes ont le mode de liaison par défaut `TwoWay` :

- Propriété `Date` de `DatePicker`
- Propriété `Text` de `Editor`, `Entry`, `SearchBar` et `EntryCell`
- Propriété `IsRefreshing` de `ListView`
- Propriété `SelectedItem` de `MultiPage`
- Propriétés `SelectedIndex` et `SelectedItem` de `Picker`
- Propriété `Value` de `Slider` et `Stepper`
- Propriété `IsToggled` de `Switch`
- Propriété `On` de `SwitchCell`
- Propriété `Time` de `TimePicker`

Ces propriétés particulières sont définies comme `TwoWay` pour une très bonne raison :

Lorsque des liaisons de données sont utilisées avec l’architecture d’application MVVM (modèle-vue-vue modèle), la classe ViewModel est la source de la liaison de données et la vue, qui se compose de vues telles que `Slider`, est la cible de la liaison de données. Les liaisons MVVM ressemblent plus à l’exemple **Reverse Binding** (Liaison inverse) que les liaisons des exemples précédents. Il est très probable que vous souhaitiez initialiser chaque vue de la page avec la valeur de la propriété correspondante dans le ViewModel, mais des modifications dans la vue doivent également affecter la propriété ViewModel.

Les propriétés dotées du mode de liaison par défaut `TwoWay` sont les plus susceptibles d’être utilisées dans les scénarios MVVM.

### <a name="one-way-to-source-bindings"></a>Liaisons unidirectionnelles vers la source

Les propriétés pouvant être liées en lecture seule disposent du mode de liaison par défaut `OneWayToSource`. Il existe une seule propriété pouvant être liée en lecture/écriture qui dispose du mode de liaison par défaut `OneWayToSource` :

- Propriété `SelectedItem` de `ListView`

Le raisonnement est qu’une liaison sur la propriété `SelectedItem` doit aboutir à la définition de la source de liaison. Un exemple qui sera présenté ultérieurement dans cet article remplace ce comportement.

### <a name="one-time-bindings"></a>Liaisons à usage unique

Plusieurs propriétés ont le mode de liaison par défaut `OneTime`, y compris la propriété `IsTextPredictionEnabled` de `Entry`.

Les propriétés cibles disposant du mode de liaison `OneTime` sont mises à jour uniquement lorsque le contexte de liaison change. Pour les liaisons sur ces propriétés cibles, cela simplifie l’infrastructure de liaison, car il n’est pas nécessaire de surveiller les modifications des propriétés sources.

## <a name="viewmodels-and-property-change-notifications"></a>Objets ViewModel et notifications de modification de propriété

La page **Simple Color Selector** (Sélecteur de couleur simple) illustre l’utilisation d’un ViewModel simple. Les liaisons de données permettent à l’utilisateur de sélectionner une couleur à l’aide de trois éléments `Slider` pour la teinte, la saturation et la luminosité.

Le ViewModel est la source de la liaison de données. Le ViewModel *ne définit pas* de propriétés pouvant être liées, mais il implémente un mécanisme de notification qui permet d’avertir l’infrastructure de liaison lorsque la valeur d’une propriété change. Ce mécanisme de notification est l’interface [`INotifyPropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged) qui définit un événement unique nommé [`PropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged.PropertyChanged). Une classe qui implémente cette interface déclenche généralement l’événement lorsque l’une de ses propriétés publiques change de valeur. L’événement n’a pas besoin d’être déclenché si la propriété ne change jamais. (L’interface `INotifyPropertyChanged` est également implémentée par `BindableObject` et un événement `PropertyChanged` est déclenché chaque fois qu’une propriété pouvant être liée change de valeur.)

La classe `HslColorViewModel` définit cinq propriétés : les propriétés `Hue`, `Saturation`, `Luminosity` et `Color` sont liées entre elles. Lorsque l’un des trois composants de couleur change de valeur, la propriété `Color` est recalculée et les événements `PropertyChanged` sont déclenchés pour les quatre propriétés :

```csharp
public class HslColorViewModel : INotifyPropertyChanged
{
    Color color;
    string name;

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

                Name = NamedColor.GetNearestColorName(color);
            }
        }
        get
        {
            return color;
        }
    }

    public string Name
    {
        private set
        {
            if (name != value)
            {
                name = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Name"));
            }
        }
        get
        {
            return name;
        }
    }
}
```

Lorsque la propriété `Color` change, la méthode statique `GetNearestColorName` dans la classe `NamedColor` (également incluse dans la solution **DataBindingDemos**) obtient la couleur nommée la plus proche et définit la propriété `Name`. Cette propriété `Name` a un accesseur `set` privé et ne peut donc pas être définie depuis l’extérieur de la classe.

Lorsqu’un ViewModel est défini comme source de liaison, l’infrastructure de liaison attache un gestionnaire à l’événement `PropertyChanged`. De cette façon, la liaison peut être avertie des modifications apportées aux propriétés et peut alors définir les propriétés cibles à partir des valeurs modifiées.

Toutefois, lorsqu’une propriété cible (ou la définition `Binding` sur une propriété cible) a le `BindingMode``OneTime`, l’infrastructure de liaison n’est pas tenue d’attacher un gestionnaire sur l’événement `PropertyChanged`. La propriété cible est mise à jour uniquement lorsque le `BindingContext` change et non pas lorsque la propriété source proprement dite change.

Le fichier XAML **Simple Color Selector** instancie le `HslColorViewModel` dans le dictionnaire de ressources de la page et initialise la propriété `Color`. La propriété `BindingContext` de l’objet `Grid` est définie sur une extension de liaison `StaticResource` pour référencer cette ressource :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.SimpleColorSelectorPage">

    <ContentPage.Resources>
        <ResourceDictionary>
            <local:HslColorViewModel x:Key="viewModel"
                                     Color="MediumTurquoise" />

            <Style TargetType="Slider">
                <Setter Property="VerticalOptions" Value="CenterAndExpand" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <Grid BindingContext="{StaticResource viewModel}">
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <BoxView Color="{Binding Color}"
                 Grid.Row="0" />

        <StackLayout Grid.Row="1"
                     Margin="10, 0">

            <Label Text="{Binding Name}"
                   HorizontalTextAlignment="Center" />

            <Slider Value="{Binding Hue}" />

            <Slider Value="{Binding Saturation}" />

            <Slider Value="{Binding Luminosity}" />
        </StackLayout>
    </Grid>
</ContentPage>
```

`BoxView`, `Label` et les trois vues `Slider` héritent du contexte de liaison de l’objet `Grid`. Ces vues sont toutes des cibles de liaison qui référencent les propriétés sources dans le ViewModel. Pour la propriété `Color` de `BoxView` et la propriété `Text` de `Label`, les liaisons de données sont `OneWay` : les propriétés dans la vue sont définies à partir des propriétés dans le ViewModel.

La propriété `Value` de `Slider`, toutefois, est `TwoWay`. Cela permet de définir chaque élément `Slider` à partir du ViewModel, ainsi que le ViewModel à partir de chaque élément `Slider`.

À la première exécution du programme, `BoxView`, `Label` et les trois éléments `Slider` sont tous définis à partir du ViewModel basé sur la propriété `Color` initiale, définie lorsque le ViewModel a été instancié. Ceci est illustré dans la capture d’écran iOS de gauche :

[![Sélecteur de couleurs simple](binding-mode-images/simplecolorselector-small.png "Sélecteur de couleurs simple")](binding-mode-images/simplecolorselector-large.png#lightbox "Sélecteur de couleurs simple")

Lorsque vous manipulez les curseurs, les `BoxView` et les `Label` sont mis à jour en conséquence, comme l’illustre la capture d’écran Android.

L’instanciation du ViewModel dans le dictionnaire de ressources est une approche courante. Il est également possible d’instancier le ViewModel dans les balises d’élément de propriété pour la propriété `BindingContext`. Dans le fichier XAML **Simple Color Selector**, essayez de supprimer le `HslColorViewModel` du dictionnaire de ressources et définissez-le sur la propriété `BindingContext` de l’objet `Grid`, comme suit :

```xaml
<Grid>
    <Grid.BindingContext>
        <local:HslColorViewModel Color="MediumTurquoise" />
    </Grid.BindingContext>

    ···

</Grid>
```

Le contexte de liaison peut être défini de diverses façons. Parfois, le fichier code-behind instancie le ViewModel et le définit sur la propriété `BindingContext` de la page. Toutes ces approches sont valides.

## <a name="overriding-the-binding-mode"></a>Remplacement du mode de liaison

Si le mode de liaison par défaut sur la propriété cible ne convient pas pour une liaison de données particulière, il est possible de le remplacer en définissant la propriété [`Mode`](xref:Xamarin.Forms.BindingBase.Mode) de `Binding` (ou la propriété [`Mode`](xref:Xamarin.Forms.Xaml.BindingExtension.Mode) de l’extension de balisage `Binding`) sur l’un des membres de l’énumération `BindingMode`.

Toutefois, la définition de la propriété `Mode` sur `TwoWay` ne fonctionne pas toujours comme prévu. Par exemple, essayez de modifier le fichier XAML **Alternative XAML Binding** pour inclure `TwoWay` dans la définition de la liaison :

```xaml
<Label Text="TEXT"
       FontSize="40"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand"
       Scale="{Binding Source={x:Reference slider},
                       Path=Value,
                       Mode=TwoWay}" />
```

On pourrait s’attendre à ce que l’élément `Slider` soit initialisé sur la valeur initiale de la propriété `Scale`, à savoir 1, mais ce n’est pas le cas. Lorsqu’une liaison `TwoWay` est initialisée, la cible est tout d’abord définie à partir de la source, ce qui signifie que la propriété `Scale` est définie sur la valeur par défaut 0 de `Slider`. Lorsque la liaison `TwoWay` est définie sur l’élément `Slider`, l’élément `Slider` est initialement défini à partir de la source.

Vous pouvez définir le mode de liaison sur `OneWayToSource` dans l’exemple **Alternative XAML Binding** (Liaison XAML alternative) :

```xaml
<Label Text="TEXT"
       FontSize="40"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand"
       Scale="{Binding Source={x:Reference slider},
                       Path=Value,
                       Mode=OneWayToSource}" />
```

Maintenant, l’élément `Slider` est initialisé à 1 (valeur par défaut de `Scale`) mais la manipulation de l’élément `Slider` n’affecte pas la propriété `Scale` ; ce n’est donc pas très utile.

> [!NOTE]
> La classe [`VisualElement`](xref:Xamarin.Forms.VisualElement) définit également les propriétés [`ScaleX`](xref:Xamarin.Forms.VisualElement.ScaleX) et [`ScaleY`](xref:Xamarin.Forms.VisualElement.ScaleY), qui peuvent redimensionner l’élément `VisualElement` dans les directions horizontale et verticale.

Une application très utile du remplacement du mode de liaison par défaut par `TwoWay` concerne la propriété `SelectedItem` de `ListView`. Le mode de liaison par défaut est `OneWayToSource`. Quand une liaison de données est définie sur la propriété `SelectedItem` pour référencer une propriété source dans un ViewModel, cette propriété source est définie à partir de la sélection `ListView`. Toutefois, dans certains cas, vous voudrez peut-être également initialiser `ListView` à partir du ViewModel.

La page **Sample Settings** (Exemples de paramètres) illustre cette technique. Cette page représente une implémentation simple des paramètres d’application, qui sont très souvent définis dans un ViewModel, tel que ce fichier `SampleSettingsViewModel` :

```csharp
public class SampleSettingsViewModel : INotifyPropertyChanged
{
    string name;
    DateTime birthDate;
    bool codesInCSharp;
    double numberOfCopies;
    NamedColor backgroundNamedColor;

    public event PropertyChangedEventHandler PropertyChanged;

    public SampleSettingsViewModel(IDictionary<string, object> dictionary)
    {
        Name = GetDictionaryEntry<string>(dictionary, "Name");
        BirthDate = GetDictionaryEntry(dictionary, "BirthDate", new DateTime(1980, 1, 1));
        CodesInCSharp = GetDictionaryEntry<bool>(dictionary, "CodesInCSharp");
        NumberOfCopies = GetDictionaryEntry(dictionary, "NumberOfCopies", 1.0);
        BackgroundNamedColor = NamedColor.Find(GetDictionaryEntry(dictionary, "BackgroundNamedColor", "White"));
    }

    public string Name
    {
        set { SetProperty(ref name, value); }
        get { return name; }
    }

    public DateTime BirthDate
    {
        set { SetProperty(ref birthDate, value); }
        get { return birthDate; }
    }

    public bool CodesInCSharp
    {
        set { SetProperty(ref codesInCSharp, value); }
        get { return codesInCSharp; }
    }

    public double NumberOfCopies
    {
        set { SetProperty(ref numberOfCopies, value); }
        get { return numberOfCopies; }
    }

    public NamedColor BackgroundNamedColor
    {
        set
        {
            if (SetProperty(ref backgroundNamedColor, value))
            {
                OnPropertyChanged("BackgroundColor");
            }
        }
        get { return backgroundNamedColor; }
    }

    public Color BackgroundColor
    {
        get { return BackgroundNamedColor?.Color ?? Color.White; }
    }

    public void SaveState(IDictionary<string, object> dictionary)
    {
        dictionary["Name"] = Name;
        dictionary["BirthDate"] = BirthDate;
        dictionary["CodesInCSharp"] = CodesInCSharp;
        dictionary["NumberOfCopies"] = NumberOfCopies;
        dictionary["BackgroundNamedColor"] = BackgroundNamedColor.Name;
    }

    T GetDictionaryEntry<T>(IDictionary<string, object> dictionary, string key, T defaultValue = default(T))
    {
        return dictionary.ContainsKey(key) ? (T)dictionary[key] : defaultValue;
    }

    bool SetProperty<T>(ref T storage, T value, [CallerMemberName] string propertyName = null)
    {
        if (object.Equals(storage, value))
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

Chaque paramètre d’application est une propriété qui est enregistrée dans le dictionnaire de propriétés de Xamarin.Forms, dans une méthode nommée `SaveState` et chargée à partir de ce dictionnaire dans le constructeur. Vers le bas de cette classe, deux méthodes aident à rationaliser les objets ViewModel et à les rendre moins sujets aux erreurs. La méthode `OnPropertyChanged` du bas possède un paramètre facultatif qui est défini sur la propriété appelante. Il permet d’éviter les fautes d’orthographe lors de la spécification du nom de la propriété sous forme de chaîne.

La méthode `SetProperty` dans la classe fait encore plus : elle compare la valeur définie sur la propriété avec la valeur stockée en tant que champ, et elle appelle `OnPropertyChanged` seulement si ces deux valeurs ne sont pas identiques.

La classe `SampleSettingsViewModel` définit deux propriétés pour la couleur d’arrière-plan : la propriété `BackgroundNamedColor` est de type `NamedColor`, qui est une classe également incluse dans la solution **DataBindingDemos**. La propriété `BackgroundColor` est de type `Color` et est obtenue à partir de la propriété `Color` de l’objet `NamedColor`.

La classe `NamedColor` utilise la réflexion .NET pour énumérer tous les champs publics statiques dans la structure `Color` de Xamarin.Forms et pour les stocker avec leurs noms dans une collection accessible à partir de la propriété statique `All` :

```csharp
public class NamedColor : IEquatable<NamedColor>, IComparable<NamedColor>
{
    // Instance members
    private NamedColor()
    {
    }

    public string Name { private set; get; }

    public string FriendlyName { private set; get; }

    public Color Color { private set; get; }

    public string RgbDisplay { private set; get; }

    public bool Equals(NamedColor other)
    {
        return Name.Equals(other.Name);
    }

    public int CompareTo(NamedColor other)
    {
        return Name.CompareTo(other.Name);
    }

    // Static members
    static NamedColor()
    {
        List<NamedColor> all = new List<NamedColor>();
        StringBuilder stringBuilder = new StringBuilder();

        // Loop through the public static fields of the Color structure.
        foreach (FieldInfo fieldInfo in typeof(Color).GetRuntimeFields())
        {
            if (fieldInfo.IsPublic &&
                fieldInfo.IsStatic &&
                fieldInfo.FieldType == typeof(Color))
            {
                // Convert the name to a friendly name.
                string name = fieldInfo.Name;
                stringBuilder.Clear();
                int index = 0;

                foreach (char ch in name)
                {
                    if (index != 0 && Char.IsUpper(ch))
                    {
                        stringBuilder.Append(' ');
                    }
                    stringBuilder.Append(ch);
                    index++;
                }

                // Instantiate a NamedColor object.
                Color color = (Color)fieldInfo.GetValue(null);

                NamedColor namedColor = new NamedColor
                {
                    Name = name,
                    FriendlyName = stringBuilder.ToString(),
                    Color = color,
                    RgbDisplay = String.Format("{0:X2}-{1:X2}-{2:X2}",
                                                (int)(255 * color.R),
                                                (int)(255 * color.G),
                                                (int)(255 * color.B))
                };

                // Add it to the collection.
                all.Add(namedColor);
            }
        }
        all.TrimExcess();
        all.Sort();
        All = all;
    }

    public static IList<NamedColor> All { private set; get; }

    public static NamedColor Find(string name)
    {
        return ((List<NamedColor>)All).Find(nc => nc.Name == name);
    }

    public static string GetNearestColorName(Color color)
    {
        double shortestDistance = 1000;
        NamedColor closestColor = null;

        foreach (NamedColor namedColor in NamedColor.All)
        {
            double distance = Math.Sqrt(Math.Pow(color.R - namedColor.Color.R, 2) +
                                        Math.Pow(color.G - namedColor.Color.G, 2) +
                                        Math.Pow(color.B - namedColor.Color.B, 2));

            if (distance < shortestDistance)
            {
                shortestDistance = distance;
                closestColor = namedColor;
            }
        }
        return closestColor.Name;
    }
}
```

La classe `App` dans le projet **DataBindingDemos** définit une propriété nommée `Settings` de type `SampleSettingsViewModel`. Cette propriété est initialisée lorsque la classe `App` est instanciée, et la méthode `SaveState` est appelée lorsque la méthode `OnSleep` est appelée :

```csharp
public partial class App : Application
{
    public App()
    {
        InitializeComponent();

        Settings = new SampleSettingsViewModel(Current.Properties);

        MainPage = new NavigationPage(new MainPage());
    }

    public SampleSettingsViewModel Settings { private set; get; }

    protected override void OnStart()
    {
        // Handle when your app starts
    }

    protected override void OnSleep()
    {
        // Handle when your app sleeps
        Settings.SaveState(Current.Properties);
    }

    protected override void OnResume()
    {
        // Handle when your app resumes
    }
}
```

Pour plus d’informations sur les méthodes de cycle de vie d’application, consultez l’article [**Cycle de vie d’application**](~/xamarin-forms/app-fundamentals/app-lifecycle.md).

Presque tout le reste est géré dans le fichier **SampleSettingsPage.xaml**. Le `BindingContext` de la page est défini à l’aide d’une extension de balisage `Binding` : la source de la liaison est la propriété statique `Application.Current`, qui est l’instance de la classe `App` dans le projet et le `Path` est défini sur la propriété `Settings`, qui est l’objet `SampleSettingsViewModel` :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.SampleSettingsPage"
             Title="Sample Settings"
             BindingContext="{Binding Source={x:Static Application.Current},
                                      Path=Settings}">

    <StackLayout BackgroundColor="{Binding BackgroundColor}"
                 Padding="10"
                 Spacing="10">

        <StackLayout Orientation="Horizontal">
            <Label Text="Name: "
                   VerticalOptions="Center" />

            <Entry Text="{Binding Name}"
                   Placeholder="your name"
                   HorizontalOptions="FillAndExpand"
                   VerticalOptions="Center" />
        </StackLayout>

        <StackLayout Orientation="Horizontal">
            <Label Text="Birth Date: "
                   VerticalOptions="Center" />

            <DatePicker Date="{Binding BirthDate}"
                        HorizontalOptions="FillAndExpand"
                        VerticalOptions="Center" />
        </StackLayout>

        <StackLayout Orientation="Horizontal">
            <Label Text="Do you code in C#? "
                   VerticalOptions="Center" />

            <Switch IsToggled="{Binding CodesInCSharp}"
                    VerticalOptions="Center" />
        </StackLayout>

        <StackLayout Orientation="Horizontal">
            <Label Text="Number of Copies: "
                   VerticalOptions="Center" />

            <Stepper Value="{Binding NumberOfCopies}"
                     VerticalOptions="Center" />

            <Label Text="{Binding NumberOfCopies}"
                   VerticalOptions="Center" />
        </StackLayout>

        <Label Text="Background Color:" />

        <ListView x:Name="colorListView"
                  ItemsSource="{x:Static local:NamedColor.All}"
                  SelectedItem="{Binding BackgroundNamedColor, Mode=TwoWay}"
                  VerticalOptions="FillAndExpand"
                  RowHeight="40">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <ViewCell>
                        <StackLayout Orientation="Horizontal">
                            <BoxView Color="{Binding Color}"
                                     HeightRequest="32"
                                     WidthRequest="32"
                                     VerticalOptions="Center" />

                            <Label Text="{Binding FriendlyName}"
                                   FontSize="24"
                                   VerticalOptions="Center" />
                        </StackLayout>                        
                    </ViewCell>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </StackLayout>
</ContentPage>
```

Tous les enfants de la page héritent du contexte de liaison. La plupart des autres liaisons de cette page se rapportent aux propriétés dans `SampleSettingsViewModel`. La propriété `BackgroundColor` est utilisée pour définir la propriété `BackgroundColor` de `StackLayout` et les propriétés `Entry`, `DatePicker`, `Switch` et `Stepper` sont toutes liées à d’autres propriétés dans le ViewModel.

La propriété `ItemsSource` de `ListView` est définie sur la propriété statique `NamedColor.All`. Cela remplit `ListView` avec toutes les instances `NamedColor`. Pour chaque élément figurant dans `ListView`, le contexte de liaison de l’élément est défini sur un objet `NamedColor`. Les objets `BoxView` et `Label` dans l’objet `ViewCell` sont liés aux propriétés figurant dans `NamedColor`.

La propriété `SelectedItem` de `ListView` est de type `NamedColor` et est liée à la propriété `BackgroundNamedColor` de `SampleSettingsViewModel` :

```xaml
SelectedItem="{Binding BackgroundNamedColor, Mode=TwoWay}"
```

Le mode de liaison par défaut pour `SelectedItem` est `OneWayToSource`, qui définit la propriété ViewModel à partir de l’élément sélectionné. Le mode `TwoWay` permet d’initialiser `SelectedItem` à partir du ViewModel.

Toutefois, lorsque `SelectedItem` est défini de cette façon, `ListView` ne défile pas automatiquement pour afficher l’élément sélectionné. Un peu de code est nécessaire dans le fichier code-behind :

```csharp
public partial class SampleSettingsPage : ContentPage
{
    public SampleSettingsPage()
    {
        InitializeComponent();

        if (colorListView.SelectedItem != null)
        {
            colorListView.ScrollTo(colorListView.SelectedItem,
                                   ScrollToPosition.MakeVisible,
                                   false);
        }
    }
}
```

La capture d’écran iOS de gauche montre le programme lors de sa première exécution. Le constructeur dans `SampleSettingsViewModel` initialise la couleur d’arrière-plan sur blanc, et c’est ce qui est sélectionné dans `ListView` :

[![Exemples de paramètres](binding-mode-images/samplesettings-small.png "Exemples de paramètres")](binding-mode-images/samplesettings-large.png#lightbox "Exemples de paramètres")

L’autre capture d’écran montre les paramètres modifiés. Lorsque vous testez cette page, veillez à mettre en veille le programme ou à l’arrêter sur l’appareil ou l’émulateur où il s’exécute. L’arrêt du programme à partir du débogueur Visual Studio n’entraîne pas le remplacement `OnSleep` dans la classe `App` à appeler.

Dans le prochain article, vous verrez comment spécifier un [**formatage de chaîne**](string-formatting.md) des liaisons de données qui sont définies sur la propriété `Text` de `Label`.

## <a name="related-links"></a>Liens connexes

- [Démonstrations de liaison de données (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)
- [Chapitre sur les liaisons de données dans la documentation de Xamarin.Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
