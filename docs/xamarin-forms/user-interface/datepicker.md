---
title: Xamarin. Forms (DatePicker)
description: Le DatePicker est un affichage Xamarin. Forms qui permet à l’utilisateur de sélectionner une date. Cet article explique comment utiliser un DatePicker dans une application Xamarin. Forms.
ms.prod: xamarin
ms.assetid: 68E8EF8A-42E7-4939-8ABE-64D060E609D9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/04/2018
ms.openlocfilehash: 15d4159d89a463c0335d9c91b24b55151c91de8c
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72695914"
---
# <a name="xamarinforms-datepicker"></a>Xamarin. Forms (DatePicker)

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-datepicker)

_Vue Xamarin. Forms qui permet à l’utilisateur de sélectionner une date._

Xamarin. Forms [`DatePicker`](xref:Xamarin.Forms.DatePicker) appelle le contrôle de sélecteur de dates de la plateforme et permet à l’utilisateur de sélectionner une date. `DatePicker` définit huit propriétés :

- [`MinimumDate`](xref:Xamarin.Forms.DatePicker.MinimumDate) de type [`DateTime`](xref:System.DateTime), qui est par défaut le premier jour de l’année 1900.
- [`MaximumDate`](xref:Xamarin.Forms.DatePicker.MaximumDate) de type `DateTime`, qui est par défaut le dernier jour de l’année 2100.
- [`Date`](xref:Xamarin.Forms.DatePicker.Date) de type `DateTime`, la date sélectionnée qui est par défaut la valeur [`DateTime.Today`](xref:System.DateTime.Today).
- [`Format`](xref:Xamarin.Forms.DatePicker.Format) de type `string`, une chaîne de mise en forme .NET [standard](/dotnet/standard/base-types/standard-date-and-time-format-strings/) ou [personnalisée](/dotnet/standard/base-types/custom-date-and-time-format-strings/) , qui a comme valeur par défaut « D », le modèle de date longue.
- [`TextColor`](xref:Xamarin.Forms.DatePicker.TextColor) de type [`Color`](xref:Xamarin.Forms.Color), la couleur utilisée pour afficher la date sélectionnée, qui est [`Color.Default`](xref:Xamarin.Forms.Color.Default)par défaut.
- [`FontAttributes`](xref:Xamarin.Forms.DatePicker.FontAttributes) de type [`FontAttributes`](xref:Xamarin.Forms.FontAttributes), qui a par défaut la valeur [`FontAtributes.None`](xref:Xamarin.Forms.FontAttributes.None).
- [`FontFamily`](xref:Xamarin.Forms.DatePicker.FontFamily) de type `string`, qui a par défaut la valeur `null`.
- [`FontSize`](xref:Xamarin.Forms.DatePicker.FontSize) de type `double`, dont la valeur par défaut est-1,0.
- `CharacterSpacing`, de type `double`, est l’espacement entre les caractères du texte de `DatePicker`.

Le `DatePicker` déclenche un événement [`DateSelected`](xref:Xamarin.Forms.DatePicker.DateSelected) lorsque l’utilisateur sélectionne une date.

> [!WARNING]
> Lorsque vous définissez `MinimumDate` et `MaximumDate`, assurez-vous que `MinimumDate` est toujours inférieur ou égal à `MaximumDate`. Sinon, `DatePicker` lève une exception.

En interne, le `DatePicker` garantit que `Date` se trouve entre `MinimumDate` et `MaximumDate`, inclus. Si `MinimumDate` ou `MaximumDate` est défini de sorte que `Date` ne soit pas entre eux, `DatePicker` ajustera la valeur de `Date`.

Les huit propriétés sont sauvegardées par des objets [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , ce qui signifie qu’ils peuvent être stylisés et que les propriétés peuvent être des cibles de liaisons de données. La propriété `Date` a un mode de liaison par défaut de [`BindingMode.TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay), ce qui signifie qu’elle peut être la cible d’une liaison de données dans une application qui utilise l’architecture [MVVM (Model-View-ViewModel)](~/xamarin-forms/enterprise-application-patterns/mvvm.md) .

## <a name="initializing-the-datetime-properties"></a>Initialisation des propriétés DateTime

Dans le code, vous pouvez initialiser les propriétés `MinimumDate`, `MaximumDate` et `Date` aux valeurs de type `DateTime` :

```csharp
DatePicker datePicker = new DatePicker
{
    MinimumDate = new DateTime(2018, 1, 1),
    MaximumDate = new DateTime(2018, 12, 31),
    Date = new DateTime(2018, 6, 21)
};
```

Quand une valeur `DateTime` est spécifiée en XAML, l’analyseur XAML utilise la méthode `DateTime.Parse` avec un argument `CultureInfo.InvariantCulture` pour convertir la chaîne en valeur `DateTime`. Les dates doivent être spécifiées dans un format précis : mois à deux chiffres, jours à deux chiffres et années à quatre chiffres séparés par des barres obliques :

```xaml
<DatePicker MinimumDate="01/01/2018"
            MaximumDate="12/31/2018"
            Date="06/21/2018" />
```

Si la propriété `BindingContext` de `DatePicker` a la valeur d’une instance d’un ViewModel contenant des propriétés de type `DateTime` nommées `MinDate`, `MaxDate` et `SelectedDate` (par exemple), vous pouvez instancier le `DatePicker` comme suit :

```xaml
<DatePicker MinimumDate="{Binding MinDate}"
            MaximumDate="{Binding MaxDate}"
            Date="{Binding SelectedDate}" />
```

Dans cet exemple, les trois propriétés sont initialisées avec les propriétés correspondantes dans le ViewModel. Étant donné que la propriété `Date` a un mode de liaison `TwoWay`, toute nouvelle date que l’utilisateur sélectionne est automatiquement reflétée dans le ViewModel.

Si le `DatePicker` ne contient pas de liaison sur sa propriété `Date`, une application doit joindre un gestionnaire à l’événement `DateSelected` pour être informé lorsque l’utilisateur sélectionne une nouvelle date.

Pour plus d’informations sur la définition des propriétés de police, consultez [polices](~/xamarin-forms/user-interface/text/fonts.md).

## <a name="datepicker-and-layout"></a>DatePicker et Layout

Il est possible d’utiliser une option de disposition horizontale sans contrainte comme `Center`, `Start` ou `End` avec `DatePicker` :

```xaml
<DatePicker ···
            HorizontalOptions="Center"
            ··· />
```

Toutefois, cela n’est pas recommandé. Selon le paramètre de la propriété `Format`, les dates sélectionnées peuvent nécessiter des largeurs d’affichage différentes. Par exemple, la chaîne de format « D » fait que `DateTime` affiche des dates dans un format long et « mercredi 12 septembre 2018 » requiert une largeur d’affichage supérieure à « vendredi, le 4 mai 2018 ». En fonction de la plateforme, cette différence peut entraîner la modification de la largeur de la vue `DateTime` dans la disposition ou la troncation de l’affichage.

> [!TIP]
> Il est préférable d’utiliser le paramètre par défaut `HorizontalOptions` de `Fill` avec `DatePicker`, et de ne pas utiliser une largeur de `Auto` lors de l’ajout de `DatePicker` dans une cellule `Grid`.

## <a name="datepicker-in-an-application"></a>DatePicker dans une application

L’exemple [**DaysBetweenDates**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-datepicker) comprend deux vues `DatePicker` sur sa page. Ils peuvent être utilisés pour sélectionner deux dates, et le programme calcule le nombre de jours entre ces dates. Le programme ne modifie pas les paramètres des propriétés `MinimumDate` et `MaximumDate`, de sorte que les deux dates doivent être comprises entre 1900 et 2100.

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

Chaque `DatePicker` est assignée à la propriété `Format` de « D » pour un format de date longue. Notez également que l’objet `endDatePicker` a une liaison qui cible sa propriété `MinimumDate`. La source de liaison est la propriété de `Date` sélectionnée de l’objet `startDatePicker`. Cela garantit que la date de fin est toujours ultérieure ou égale à la date de début. En plus des deux objets `DatePicker`, un `Switch` est étiqueté « inclure les deux jours au total ».

Les deux vues de `DatePicker` ont des gestionnaires attachés à l’événement `DateSelected`, et le `Switch` a un gestionnaire attaché à son événement `Toggled`. Ces gestionnaires d’événements se trouvent dans le fichier code-behind et déclenchent un nouveau calcul des jours entre les deux dates :

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

Lorsque l’exemple est exécuté pour la première fois, les deux `DatePicker` vues sont initialisées à la date du jour. La capture d’écran suivante montre le programme en cours d’exécution sur iOS, Android et le plateforme Windows universelle :

[![Jours entre les dates de début](datepicker-images/DaysBetweenDatesStart.png "Jours entre les dates de début")](datepicker-images/DaysBetweenDatesStart-Large.png#lightbox "Jours entre les dates de début")

En appuyant sur l’un des `DatePicker` affichages, appelle le sélecteur de dates de plateforme. Les plateformes implémentent ce sélecteur de dates de manière très différente, mais chaque approche est familière aux utilisateurs de cette plateforme :

[![Jours entre les dates Select](datepicker-images/DaysBetweenDatesSelect.png "Jours entre les dates Select")](datepicker-images/DaysBetweenDatesSelect-Large.png#lightbox "Jours entre les dates Select")

> [!TIP]
> Sur Android, la boîte de dialogue `DatePicker` peut être personnalisée en remplaçant la méthode `CreateDatePickerDialog` dans un convertisseur personnalisé. Cela permet, par exemple, l’ajout de boutons supplémentaires à la boîte de dialogue.

Une fois deux dates sélectionnées, l’application affiche le nombre de jours entre ces dates :

[![Résultat des jours entre les dates](datepicker-images/DaysBetweenDatesResult.png "Résultat des jours entre les dates")](datepicker-images/DaysBetweenDatesResult-Large.png#lightbox "Résultat des jours entre les dates")

## <a name="related-links"></a>Liens connexes

- [Exemple DaysBetweenDates](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-datepicker)
- [API DatePicker](xref:Xamarin.Forms.DatePicker)
