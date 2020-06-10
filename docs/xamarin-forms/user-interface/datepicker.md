---
title : " Xamarin.Forms DatePicker" Description : "le DatePicker est une Xamarin.Forms vue qui permet à l’utilisateur de sélectionner une date. Cet article explique comment utiliser un DatePicker dans une Xamarin.Forms application.
ms. Prod : xamarin ms. AssetID : 68E8EF8A-42E7-4939-8ABE-64D060E609D9 ms. Technology : xamarin-Forms Author : davidbritch ms. Author : dabritch ms. Date : 06/04/2018 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-datepicker"></a>Xamarin.FormsDatePicker

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-datepicker)

_Xamarin.FormsVue qui permet à l’utilisateur de sélectionner une date._

Xamarin.Forms [`DatePicker`](xref:Xamarin.Forms.DatePicker) Appelle le contrôle de sélecteur de dates de la plateforme et permet à l’utilisateur de sélectionner une date. `DatePicker`définit huit propriétés :

- [`MinimumDate`](xref:Xamarin.Forms.DatePicker.MinimumDate)de type [`DateTime`](xref:System.DateTime) , qui a comme valeur par défaut le premier jour de l’année 1900.
- [`MaximumDate`](xref:Xamarin.Forms.DatePicker.MaximumDate)de type `DateTime` , qui a comme valeur par défaut le dernier jour de l’année 2100.
- [`Date`](xref:Xamarin.Forms.DatePicker.Date)de type `DateTime` , date sélectionnée, qui a comme valeur par défaut la valeur [`DateTime.Today`](xref:System.DateTime.Today) .
- [`Format`](xref:Xamarin.Forms.DatePicker.Format)de type `string` , une chaîne de mise en forme .NET [standard](/dotnet/standard/base-types/standard-date-and-time-format-strings/) ou [personnalisée](/dotnet/standard/base-types/custom-date-and-time-format-strings/) , qui a comme valeur par défaut « D », le modèle de date longue.
- [`TextColor`](xref:Xamarin.Forms.DatePicker.TextColor)de type [`Color`](xref:Xamarin.Forms.Color) , la couleur utilisée pour afficher la date sélectionnée, qui a comme valeur par défaut [`Color.Default`](xref:Xamarin.Forms.Color.Default) .
- [`FontAttributes`](xref:Xamarin.Forms.DatePicker.FontAttributes)de type [`FontAttributes`](xref:Xamarin.Forms.FontAttributes) , qui a comme valeur par défaut [`FontAtributes.None`](xref:Xamarin.Forms.FontAttributes.None) .
- [`FontFamily`](xref:Xamarin.Forms.DatePicker.FontFamily)de type `string` , qui a comme valeur par défaut `null` .
- [`FontSize`](xref:Xamarin.Forms.DatePicker.FontSize)de type `double` , qui a comme valeur par défaut-1,0.
- `CharacterSpacing`, de type `double` , est l’espacement entre les caractères du `DatePicker` texte.

Le `DatePicker` déclenche un [`DateSelected`](xref:Xamarin.Forms.DatePicker.DateSelected) événement lorsque l’utilisateur sélectionne une date.

> [!WARNING]
> Lors de la définition de et de, assurez-vous `MinimumDate` `MaximumDate` que `MinimumDate` est toujours inférieur ou égal à `MaximumDate` . Sinon, `DatePicker` lève une exception.

En interne, la `DatePicker` s’assure que `Date` est compris entre `MinimumDate` et `MaximumDate` , inclus. Si `MinimumDate` ou `MaximumDate` est défini de telle sorte qu' `Date` il n’est pas compris entre eux, `DatePicker` ajuste la valeur de `Date` .

Les huit propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie qu’elles peuvent être stylisées et que les propriétés peuvent être des cibles de liaisons de données. La `Date` propriété a un mode de liaison par défaut de, ce qui [`BindingMode.TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) signifie qu’elle peut être la cible d’une liaison de données dans une application qui utilise l’architecture [MVVM (Model-View-ViewModel)](~/xamarin-forms/enterprise-application-patterns/mvvm.md) .

## <a name="initializing-the-datetime-properties"></a>Initialisation des propriétés DateTime

Dans le code, vous pouvez initialiser `MinimumDate` les `MaximumDate` Propriétés, et `Date` avec des valeurs de type `DateTime` :

```csharp
DatePicker datePicker = new DatePicker
{
    MinimumDate = new DateTime(2018, 1, 1),
    MaximumDate = new DateTime(2018, 12, 31),
    Date = new DateTime(2018, 6, 21)
};
```

Quand une `DateTime` valeur est spécifiée en XAML, l’analyseur XAML utilise la `DateTime.Parse` méthode avec un `CultureInfo.InvariantCulture` argument pour convertir la chaîne en `DateTime` valeur. Les dates doivent être spécifiées dans un format précis : mois à deux chiffres, jours à deux chiffres et années à quatre chiffres séparés par des barres obliques :

```xaml
<DatePicker MinimumDate="01/01/2018"
            MaximumDate="12/31/2018"
            Date="06/21/2018" />
```

Si la `BindingContext` propriété de `DatePicker` a la valeur d’une instance d’un ViewModel contenant des propriétés de type `DateTime` nommées `MinDate` , `MaxDate` et `SelectedDate` (par exemple), vous pouvez instancier le `DatePicker` comme suit :

```xaml
<DatePicker MinimumDate="{Binding MinDate}"
            MaximumDate="{Binding MaxDate}"
            Date="{Binding SelectedDate}" />
```

Dans cet exemple, les trois propriétés sont initialisées avec les propriétés correspondantes dans le ViewModel. Étant donné que la `Date` propriété a le mode de liaison `TwoWay` , toute nouvelle date que l’utilisateur sélectionne est automatiquement reflétée dans le ViewModel.

Si `DatePicker` ne contient pas de liaison sur sa `Date` propriété, une application doit joindre un gestionnaire à l' `DateSelected` événement pour être informé lorsque l’utilisateur sélectionne une nouvelle date.

Pour plus d’informations sur la définition des propriétés de police, consultez [polices](~/xamarin-forms/user-interface/text/fonts.md).

## <a name="datepicker-and-layout"></a>DatePicker et Layout

Il est possible d’utiliser une option de disposition horizontale sans contrainte, telle que `Center` , `Start` ou `End` avec `DatePicker` :

```xaml
<DatePicker ···
            HorizontalOptions="Center"
            ··· />
```

Toutefois, cela n’est pas recommandé. Selon le paramètre de la `Format` propriété, les dates sélectionnées peuvent nécessiter des largeurs d’affichage différentes. Par exemple, la chaîne de format « D » indique `DateTime` à d’afficher les dates dans un format long et « mercredi 12 septembre 2018 » requiert une largeur d’affichage supérieure à « vendredi, le 4 mai 2018 ». Selon la plateforme, cette différence peut entraîner la modification de la `DateTime` largeur de la vue dans la disposition ou la troncation de l’affichage.

> [!TIP]
> Il est préférable d’utiliser le `HorizontalOptions` paramètre par défaut `Fill` avec `DatePicker` et non pour utiliser une largeur de lors de la `Auto` mise en place d' `DatePicker` une `Grid` cellule.

## <a name="datepicker-in-an-application"></a>DatePicker dans une application

L’exemple [**DaysBetweenDates**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-datepicker) comprend deux `DatePicker` vues sur sa page. Ils peuvent être utilisés pour sélectionner deux dates, et le programme calcule le nombre de jours entre ces dates. Le programme ne modifie pas les paramètres des `MinimumDate` `MaximumDate` Propriétés et, de sorte que les deux dates doivent être comprises entre 1900 et 2100.

Voici le fichier XAML :

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DaysBetweenDates"
             x:Class="DaysBetweenDates.MainPage">
    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="iOS" Value="0, 20, 0, 0" />
        </OnPlatform>
    </ContentPage.Padding>

    <StackLayout Margin="10">
        <Label Text="Days Between Dates"
               Style="{DynamicResource TitleStyle}"
               Margin="0, 20"
               HorizontalTextAlignment="Center" />

        <Label Text="Start Date:" />

        <DatePicker x:Name="startDatePicker"
                    Format="D"
                    Margin="30, 0, 0, 30"
                    DateSelected="OnDateSelected" />

        <Label Text="End Date:" />

        <DatePicker x:Name="endDatePicker"
                    MinimumDate="{Binding Source={x:Reference startDatePicker},
                                          Path=Date}"
                    Format="D"
                    Margin="30, 0, 0, 30"
                    DateSelected="OnDateSelected" />

        <StackLayout Orientation="Horizontal"
                     Margin="0, 0, 0, 30">
            <Label Text="Include both days in total: "
                   VerticalOptions="Center" />
            <Switch x:Name="includeSwitch"
                    Toggled="OnSwitchToggled" />
        </StackLayout>

        <Label x:Name="resultLabel"
               FontAttributes="Bold"
               HorizontalTextAlignment="Center" />

    </StackLayout>
</ContentPage>
```

Chaque `DatePicker` est assignée `Format` à une propriété de « D » pour un format de date longue. Notez également que l' `endDatePicker` objet a une liaison qui cible sa `MinimumDate` propriété. La source de liaison est la `Date` propriété sélectionnée de l' `startDatePicker` objet. Cela garantit que la date de fin est toujours ultérieure ou égale à la date de début. En plus des deux `DatePicker` objets, un `Switch` est étiqueté « inclure les deux jours au total ».

Les deux `DatePicker` vues ont des gestionnaires attachés à l' `DateSelected` événement, et le `Switch` possède un gestionnaire attaché à son `Toggled` événement. Ces gestionnaires d’événements se trouvent dans le fichier code-behind et déclenchent un nouveau calcul des jours entre les deux dates :

```csharp
public partial class MainPage : ContentPage
{
    public MainPage()
    {
        InitializeComponent();
    }

    void OnDateSelected(object sender, DateChangedEventArgs args)
    {
        Recalculate();
    }

    void OnSwitchToggled(object sender, ToggledEventArgs args)
    {
        Recalculate();
    }

    void Recalculate()
    {
        TimeSpan timeSpan = endDatePicker.Date - startDatePicker.Date +
            (includeSwitch.IsToggled ? TimeSpan.FromDays(1) : TimeSpan.Zero);

        resultLabel.Text = String.Format("{0} day{1} between dates",
                                            timeSpan.Days, timeSpan.Days == 1 ? "" : "s");
    }
}
```

Lorsque l’exemple est exécuté pour la première fois, les deux `DatePicker` vues sont initialisées à la date du jour. La capture d’écran suivante montre le programme s’exécutant sur iOS et Android :

[![Jours entre les dates de début](datepicker-images/DaysBetweenDatesStart.png "Jours entre les dates de début")](datepicker-images/DaysBetweenDatesStart-Large.png#lightbox "Jours entre les dates de début")

En appuyant sur l’un des `DatePicker` affichages, vous appelez le sélecteur de dates de plateforme. Les plateformes implémentent ce sélecteur de dates de manière très différente, mais chaque approche est familière aux utilisateurs de cette plateforme :

[![Jours entre les dates Select](datepicker-images/DaysBetweenDatesSelect.png "Jours entre les dates Select")](datepicker-images/DaysBetweenDatesSelect-Large.png#lightbox "Jours entre les dates Select")

> [!TIP]
> Sur Android, la `DatePicker` boîte de dialogue peut être personnalisée en substituant la `CreateDatePickerDialog` méthode dans un convertisseur personnalisé. Cela permet, par exemple, l’ajout de boutons supplémentaires à la boîte de dialogue.

Une fois deux dates sélectionnées, l’application affiche le nombre de jours entre ces dates :

[![Résultat des jours entre les dates](datepicker-images/DaysBetweenDatesResult.png "Résultat des jours entre les dates")](datepicker-images/DaysBetweenDatesResult-Large.png#lightbox "Résultat des jours entre les dates")

## <a name="related-links"></a>Liens connexes

- [Exemple DaysBetweenDates](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-datepicker)
- [API DatePicker](xref:Xamarin.Forms.DatePicker)
