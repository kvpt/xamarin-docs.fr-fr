---
title: Xamarin.Forms DatePicker
description: Le sélecteur de dates est une vue de Xamarin.Forms qui permet à l’utilisateur de sélectionner une date. Cet article explique comment utiliser un sélecteur de dates dans une application Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 68E8EF8A-42E7-4939-8ABE-64D060E609D9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/04/2018
ms.openlocfilehash: 521df49a7698bc149d6bca7460cff2df74402bda
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68657021"
---
# <a name="xamarinforms-datepicker"></a>Xamarin.Forms DatePicker

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-datepicker)

_Une vue de Xamarin.Forms qui permet à l’utilisateur de sélectionner une date._

Xamarin.Forms [ `DatePicker` ](xref:Xamarin.Forms.DatePicker) appelle le contrôle de sélecteur de dates de la plateforme et permet à l’utilisateur de sélectionner une date. `DatePicker` définit des huit propriétés :

- [`MinimumDate`](xref:Xamarin.Forms.DatePicker.MinimumDate) de type [ `DateTime` ](xref:System.DateTime), qui est par défaut pour le premier jour de l’année 1900.
- [`MaximumDate`](xref:Xamarin.Forms.DatePicker.MaximumDate) de type `DateTime`, les valeurs par défaut et le dernier jour de l’année 2100.
- [`Date`](xref:Xamarin.Forms.DatePicker.Date) de type `DateTime`, la date sélectionnée par défaut, la valeur [ `DateTime.Today` ](xref:System.DateTime.Today).
- [`Format`](xref:Xamarin.Forms.DatePicker.Format) de type `string`, un [standard](/dotnet/standard/base-types/standard-date-and-time-format-strings/) ou [personnalisé](/dotnet/standard/base-types/custom-date-and-time-format-strings/) .NET mise en forme de chaîne, qui par défaut est « D », modèle de date du long.
- [`TextColor`](xref:Xamarin.Forms.DatePicker.TextColor) de type [ `Color` ](xref:Xamarin.Forms.Color), la couleur utilisée pour afficher la date sélectionnée par défaut [ `Color.Default` ](xref:Xamarin.Forms.Color.Default).
- [`FontAttributes`](xref:Xamarin.Forms.DatePicker.FontAttributes) de type [ `FontAttributes` ](xref:Xamarin.Forms.FontAttributes), qui utilise par défaut [ `FontAtributes.None` ](xref:Xamarin.Forms.FontAttributes.None).
- [`FontFamily`](xref:Xamarin.Forms.DatePicker.FontFamily) de type `string`, qui utilise par défaut `null`.
- [`FontSize`](xref:Xamarin.Forms.DatePicker.FontSize) de type `double`, qui est par défaut de -1,0.

Le `DatePicker` se déclenche un [ `DateSelected` ](xref:Xamarin.Forms.DatePicker.DateSelected) événement lorsque l’utilisateur sélectionne une date.

> [!WARNING]
> Lors de la définition `MinimumDate` et `MaximumDate`, assurez-vous que l’option `MinimumDate` est toujours inférieure ou égale à `MaximumDate`. Sinon, `DatePicker` lève une exception.

En interne, le `DatePicker` garantit que `Date` entre `MinimumDate` et `MaximumDate`, inclusif. Si `MinimumDate` ou `MaximumDate` est défini afin que `Date` n’est pas entre eux, `DatePicker` ajustera la valeur de `Date`.

Toutes les propriétés de huit sont soutenues par [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie que des styles peuvent, et les propriétés peuvent être des cibles de liaisons de données. Le `Date` propriété a un mode de liaison par défaut de [ `BindingMode.TwoWay` ](xref:Xamarin.Forms.BindingMode.TwoWay), ce qui signifie qu’il peut être une cible d’une liaison de données dans une application qui utilise le [Model-View-ViewModel (MVVM)](~/xamarin-forms/enterprise-application-patterns/mvvm.md) architecture.

## <a name="initializing-the-datetime-properties"></a>Initialiser les propriétés de date/heure

Dans le code, vous pouvez initialiser le `MinimumDate`, `MaximumDate`, et `Date` propriétés aux valeurs de type `DateTime`:

```csharp
DatePicker datePicker = new DatePicker
{
    MinimumDate = new DateTime(2018, 1, 1),
    MaximumDate = new DateTime(2018, 12, 31),
    Date = new DateTime(2018, 6, 21)
};
```

Quand un `DateTime` est spécifiée dans XAML, l’analyseur XAML utilise le `DateTime.Parse` méthode avec un `CultureInfo.InvariantCulture` argument à convertir la chaîne en un `DateTime` valeur. Les dates doivent être spécifiées dans un format précis : les mois à deux chiffres, les jours à deux chiffres et les années à quatre chiffres séparées par des barres obliques :

```xaml
<DatePicker MinimumDate="01/01/2018"
            MaximumDate="12/31/2018"
            Date="06/21/2018" />
```

Si le `BindingContext` propriété du `DatePicker` est définie sur une instance d’un ViewModel contenant les propriétés de type `DateTime` nommé `MinDate`, `MaxDate`, et `SelectedDate` (par exemple), vous pouvez instancier la `DatePicker` comme suit :

```xaml
<DatePicker MinimumDate="{Binding MinDate}"
            MaximumDate="{Binding MaxDate}"
            Date="{Binding SelectedDate}" />
```

Dans cet exemple, toutes les trois propriétés sont initialisées dans les propriétés correspondantes dans le ViewModel. Étant donné que le `Date` propriété dispose d’un mode de liaison de `TwoWay`, toute nouvelle date sélectionné par l’utilisateur est automatiquement répercutée dans le ViewModel.

Si le `DatePicker` ne contient pas d’une liaison sur sa `Date` propriété, une application doit attacher un gestionnaire pour le `DateSelected` événement doit être informé lorsque l’utilisateur sélectionne une nouvelle date.

Pour plus d’informations sur la définition des propriétés de police, consultez [polices](~/xamarin-forms/user-interface/text/fonts.md).

## <a name="datepicker-and-layout"></a>Sélecteur de dates et la disposition

Il est possible d’utiliser une option de disposition horizontale sans contrainte comme `Center`, `Start`, ou `End` avec `DatePicker`:

```xaml
<DatePicker ···
            HorizontalOptions="Center"
            ··· />
```

Toutefois, cela n’est pas recommandé. Selon le paramètre de la `Format` propriété, sélectionnée dates peuvent nécessiter des largeurs d’affichage différent. Par exemple, la chaîne de format « D », `DateTime` pour afficher des dates dans un format long et « Mercredi 12 septembre 2018 » requiert une largeur d’affichage supérieure à « Vendredi 4 mai 2018 ». Selon la plateforme, cette différence peut entraîner la `DateTime` vue pour modifier la largeur de disposition, ou pour l’affichage à tronquer.

> [!TIP]
> Il est préférable d’utiliser la valeur par défaut `HorizontalOptions` paramètre `Fill` avec `DatePicker`et non une largeur de `Auto` lors du placement de `DatePicker` dans un `Grid` cellule.

## <a name="datepicker-in-an-application"></a>Sélecteur de dates dans une application

Le [ **DaysBetweenDates** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-datepicker) exemple inclut deux `DatePicker` vues sur sa page. Ceux-ci peuvent être utilisés pour sélectionner deux dates, et le programme calcule le nombre de jours entre ces dates. Le programme ne change pas les paramètres de la `MinimumDate` et `MaximumDate` propriétés, de sorte que les deux dates doivent être comprise entre 1900 et 2100.

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

Chaque `DatePicker` est affecté un `Format` propriété de « D » pour un format de date longue. Notez également que le `endDatePicker` objet a une liaison qui cible son `MinimumDate` propriété. La source de liaison est sélectionné `Date` propriété de la `startDatePicker` objet. Cela garantit que la date de fin est toujours plus tard ou égale à la date de début. En plus des deux `DatePicker` objets, un `Switch` est étiqueté « Inclure les deux jours au total ».

Les deux `DatePicker` les vues ont les gestionnaires attachés à la `DateSelected` événement et le `Switch` a un gestionnaire attaché à son `Toggled` événement. Ces gestionnaires d’événements se trouvent dans le fichier code-behind et déclenchent un nouveau calcul des jours entre les deux dates :

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

Lorsque l’exemple est tout d’abord exécuté, les deux `DatePicker` vues sont initialisés à la date d’aujourd'hui. La capture d’écran suivante montre le programme en cours d’exécution sur iOS, Android et la plateforme Windows universelle :

[![Jours entre les Dates de début](datepicker-images/DaysBetweenDatesStart.png "jours entre les Dates de début")](datepicker-images/DaysBetweenDatesStart-Large.png#lightbox "jours entre les Dates de début")

En appuyant sur un de le `DatePicker` affiche appelle le sélecteur de dates de plateforme. Les plateformes d’implémentent ce sélecteur de dates de façons très différentes, mais chaque approche est familière aux utilisateurs de cette plateforme :

[![Sélectionnez les jours entre les Dates](datepicker-images/DaysBetweenDatesSelect.png "jours entre les Dates sélectionnez")](datepicker-images/DaysBetweenDatesSelect-Large.png#lightbox "sélectionnez jours entre les Dates")

> [!TIP]
> Sur Android, le `DatePicker` boîte de dialogue peut être personnalisée en substituant la `CreateDatePickerDialog` méthode dans un convertisseur personnalisé. Cela permet, par exemple, les boutons supplémentaires à ajouter à la boîte de dialogue.

Une fois les deux dates sont sélectionnés, l’application affiche le nombre de jours entre ces dates :

[![Jours entre les Dates résultat](datepicker-images/DaysBetweenDatesResult.png "jours entre les Dates résultat")](datepicker-images/DaysBetweenDatesResult-Large.png#lightbox "jours entre le résultat de Dates")

## <a name="related-links"></a>Liens connexes

- [Exemple de DaysBetweenDates](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-datepicker)
- [DatePicker API](xref:Xamarin.Forms.DatePicker)
