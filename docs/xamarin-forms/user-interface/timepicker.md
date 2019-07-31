---
title: Xamarin.Forms TimePicker
description: TimePicker est un affichage Xamarin. Forms qui permet à l’utilisateur de sélectionner une heure. Cet article explique comment utiliser un TimePicker dans une application Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 2E99FB23-B82D-4EB4-AFB3-5002E736E7B2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/16/2018
ms.openlocfilehash: d5c4cc6600c8192718257abf4ef1cbec49c12eee
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656486"
---
# <a name="xamarinforms-timepicker"></a>Xamarin.Forms TimePicker

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-timepicker)

_Une vue de Xamarin.Forms qui permet à l’utilisateur à sélectionner une heure._

Xamarin.Forms [ `TimePicker` ](xref:Xamarin.Forms.TimePicker) appelle le contrôle de sélecteur d’heure de la plateforme et permet à l’utilisateur à sélectionner une heure. `TimePicker` définit les propriétés suivantes :

- [`Time`](xref:Xamarin.Forms.TimePicker.Time) de type `TimeSpan`, l’heure sélectionnée, qui est par défaut pour un `TimeSpan` de 0. Le `TimeSpan` type indique une durée de la durée écoulée depuis minuit.
- [`Format`](xref:Xamarin.Forms.TimePicker.Format) de type `string`, un [standard](/dotnet/standard/base-types/standard-date-and-time-format-strings/) ou [personnalisé](/dotnet/standard/base-types/custom-date-and-time-format-strings/) .NET mise en forme de chaîne, qui par défaut est « t », le modèle d’heure courte.
- [`TextColor`](xref:Xamarin.Forms.TimePicker.TextColor) de type [ `Color` ](xref:Xamarin.Forms.Color), la couleur utilisée pour afficher l’heure sélectionnée, qui utilise par défaut [ `Color.Default` ](xref:Xamarin.Forms.Color.Default).
- [`FontAttributes`](xref:Xamarin.Forms.TimePicker.FontAttributes) de type [ `FontAttributes` ](xref:Xamarin.Forms.FontAttributes), qui utilise par défaut [ `FontAtributes.None` ](xref:Xamarin.Forms.FontAttributes.None).
- [`FontFamily`](xref:Xamarin.Forms.TimePicker.FontFamily) de type `string`, qui utilise par défaut `null`.
- [`FontSize`](xref:Xamarin.Forms.TimePicker.FontSize) de type `double`, qui est par défaut de -1,0.

Ces propriétés sont soutenus par [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie que des styles peuvent, et les propriétés peuvent être des cibles de liaisons de données. Le [ `Time` ](xref:Xamarin.Forms.TimePicker.Time) propriété a un mode de liaison par défaut de [ `BindingMode.TwoWay` ](xref:Xamarin.Forms.BindingMode.TwoWay), ce qui signifie qu’il peut être une cible d’une liaison de données dans une application qui utilise le [ Model-View-ViewModel (MVVM)](~/xamarin-forms/enterprise-application-patterns/mvvm.md) architecture.

Le [ `TimePicker` ](xref:Xamarin.Forms.TimePicker) n’inclut pas un événement pour indiquer un nouveau sélectionné [ `Time` ](xref:Xamarin.Forms.TimePicker.Time) valeur. Si vous avez besoin être averti de celle-ci, vous pouvez ajouter un gestionnaire pour le [ `PropertyChanged` ](xref:Xamarin.Forms.BindableObject.PropertyChanged) événement.

## <a name="initializing-the-time-property"></a>L’initialisation de la propriété de temps

Dans le code, vous pouvez initialiser le [ `Time` ](xref:Xamarin.Forms.TimePicker.Time) propriété une valeur de type `TimeSpan`:

```csharp
TimePicker timePicker = new TimePicker
{
  Time = new TimeSpan(4, 15, 26) // Time set to "04:15:26"
};
```

Lorsque le [ `Time` ](xref:Xamarin.Forms.TimePicker.Time) propriété est spécifiée dans XAML, la valeur est convertie en un `TimeSpan` et validées pour garantir que le nombre de millisecondes est supérieur ou égal à 0, et que le nombre d’heures est inférieure à 24. Les composants heure doivent être séparés par le signe deux-points :

```xaml
<TimePicker Time="4:15:26" />
```

Si le [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) propriété du [ `TimePicker` ](xref:Xamarin.Forms.TimePicker) est définie sur une instance d’un ViewModel contenant une propriété de type `TimeSpan` nommé `SelectedTime` (par exemple), vous pouvez instancier le `TimePicker` comme suit :

```xaml
<TimePicker Time="{Binding SelectedTime}" />
```

Dans cet exemple, le [ `Time` ](xref:Xamarin.Forms.TimePicker.Time) propriété est initialisée avec le `SelectedTime` propriété dans le ViewModel. Étant donné que le `Time` propriété dispose d’un mode de liaison de [ `TwoWay` ](xref:Xamarin.Forms.BindingMode.TwoWay), toute nouvelle heure sélectionné par l’utilisateur est automatiquement propagée vers le ViewModel.

Si le [ `TimePicker` ](xref:Xamarin.Forms.TimePicker) ne contient pas d’une liaison sur sa [ `Time` ](xref:Xamarin.Forms.TimePicker.Time) propriété, une application doit attacher un gestionnaire pour le [ `PropertyChanged` ](xref:Xamarin.Forms.BindableObject.PropertyChanged) événement être informé lorsque l’utilisateur sélectionne une nouvelle heure.

Pour plus d’informations sur la définition des propriétés de police, consultez [polices](~/xamarin-forms/user-interface/text/fonts.md).

## <a name="timepicker-and-layout"></a>TimePicker et la disposition

Il est possible d’utiliser une option de disposition horizontale sans contrainte comme `Center`, `Start`, ou `End` avec [ `TimePicker` ](xref:Xamarin.Forms.TimePicker):

```xaml
<TimePicker ···
            HorizontalOptions="Center"
            ··· />
```

Toutefois, cela n’est pas recommandé. Selon le paramètre de la [ `Format` ](xref:Xamarin.Forms.TimePicker.Format) propriété, sélectionnée fois peuvent nécessiter des largeurs d’affichage différent. Par exemple, la chaîne de format « T », le [ `TimePicker` ](xref:Xamarin.Forms.TimePicker) afficher pour afficher le temps dans un format long, et « 4:15:26 AM » requiert une largeur d’affichage supérieure que le format d’heure courte (« t ») de « 4:15 AM ». Selon la plateforme, cette différence peut entraîner la `TimePicker` vue pour modifier la largeur de disposition, ou pour l’affichage à tronquer.

> [!TIP]
> Il est préférable d’utiliser la valeur par défaut `HorizontalOptions` paramètre `Fill` avec [ `TimePicker` ](xref:Xamarin.Forms.TimePicker)et non une largeur de `Auto` lors du placement de `TimePicker` dans un `Grid` cellule.

## <a name="timepicker-in-an-application"></a>TimePicker dans une application

Le [ **SetTimer** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-timepicker) exemple inclut [ `TimePicker` ](xref:Xamarin.Forms.TimePicker), [ `Entry` ](xref:Xamarin.Forms.Entry), et [ `Switch` ](xref:Xamarin.Forms.Switch) vues sur sa page. Le `TimePicker` peut être utilisé pour sélectionner une heure, et que le temps lorsque se produit une boîte de dialogue alerte s’affiche qui rappelle à l’utilisateur du texte dans le `Entry`, fourni le `Switch` est basculée sur. Voici le fichier XAML :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:SetTimer"
             x:Class="SetTimer.MainPage">
    <StackLayout>
        ...
        <Entry x:Name="_entry"
               Placeholder="Enter event to be reminded of" />
        <Label Text="Select the time below to be reminded at." />
        <TimePicker x:Name="_timePicker"
                    Time="11:00:00"
                    Format="T"
                    PropertyChanged="OnTimePickerPropertyChanged" />
        <StackLayout Orientation="Horizontal">
            <Label Text="Enable timer:" />
            <Switch x:Name="_switch"
                    HorizontalOptions="EndAndExpand"
                    Toggled="OnSwitchToggled" />
        </StackLayout>
    </StackLayout>
</ContentPage>
```

Le [ `Entry` ](xref:Xamarin.Forms.Entry) vous permet d’entrer le texte de rappel qui s’affichera lorsque l’heure sélectionnée se produit. Le [ `TimePicker` ](xref:Xamarin.Forms.TimePicker) est affecté un [ `Format` ](xref:Xamarin.Forms.TimePicker.Format) propriété de « T » pour le format d’heure longue. Il possède un gestionnaire d’événements attaché à la [ `PropertyChanged` ](xref:Xamarin.Forms.BindableObject.PropertyChanged) événement et le [ `Switch` ](xref:Xamarin.Forms.Switch) a un gestionnaire attaché à son [ `Toggled` ](xref:Xamarin.Forms.Switch.Toggled) événement. Ces gestionnaires d’événements sont dans le fichier code-behind et appelez le `SetTriggerTime` méthode :

```csharp
public partial class MainPage : ContentPage
{
    DateTime _triggerTime;

    public MainPage()
    {
        InitializeComponent();

        Device.StartTimer(TimeSpan.FromSeconds(1), OnTimerTick);
    }

    bool OnTimerTick()
    {
        if (_switch.IsToggled && DateTime.Now >= _triggerTime)
        {
            _switch.IsToggled = false;
            DisplayAlert("Timer Alert", "The '" + _entry.Text + "' timer has elapsed", "OK");
        }
        return true;
    }

    void OnTimePickerPropertyChanged(object sender, PropertyChangedEventArgs args)
    {
        if (args.PropertyName == "Time")
        {
            SetTriggerTime();
        }
    }

    void OnSwitchToggled(object sender, ToggledEventArgs args)
    {
        SetTriggerTime();
    }

    void SetTriggerTime()
    {
        if (_switch.IsToggled)
        {
            _triggerTime = DateTime.Today + _timePicker.Time;
            if (_triggerTime < DateTime.Now)
            {
                _triggerTime += TimeSpan.FromDays(1);
            }
        }
    }
}
```

Le `SetTriggerTime` méthode calcule une heure du minuteur sur le `DateTime.Today` valeur de propriété et la `TimeSpan` valeur retournée à partir de la [ `TimePicker` ](xref:Xamarin.Forms.TimePicker). Cela est nécessaire, car le `DateTime.Today` propriété retourne un `DateTime` indiquant la date actuelle, mais avec une heure de minuit. Si la durée du minuteur est déjà passée dès aujourd'hui, il est supposé pour être demain.

Les graduations de minuterie chaque seconde, l’exécution de la `OnTimerTick` méthode qui vérifie si le [ `Switch` ](xref:Xamarin.Forms.Switch) est on et indique si l’heure actuelle est supérieure à ou égal à l’heure de la minuterie. Lorsque la durée du minuteur se produit, le [ `DisplayAlert` ](xref:Xamarin.Forms.Page.DisplayAlert*) méthode présente une boîte de dialogue alerte à l’utilisateur en guise de rappel.

Lors de la première exécution de l’exemple, le [ `TimePicker` ](xref:Xamarin.Forms.TimePicker) vue est initialisée à 11 : 00. En appuyant sur le `TimePicker` appelle le sélecteur d’heure de plateforme. Les plateformes d’implémentent le sélecteur d’heure de façons très différentes, mais chaque approche est familière aux utilisateurs de cette plateforme :

[![Sélectionnez heure](timepicker-images/timepicker-open.png "sélectionnez heure")](timepicker-images/timepicker-open-large.png#lightbox "sélectionnez heure")

> [!TIP]
> Sur Android, le [ `TimePicker` ](xref:Xamarin.Forms.TimePicker) boîte de dialogue peut être personnalisée en substituant la `CreateTimePickerDialog` méthode dans un convertisseur personnalisé. Cela permet, par exemple, les boutons supplémentaires à ajouter à la boîte de dialogue.

Après avoir sélectionné une heure, l’heure sélectionnée s’affiche dans le [ `TimePicker` ](xref:Xamarin.Forms.TimePicker):

[![Période sélectionnée](timepicker-images/timepicker-selected.png "période sélectionnée")](timepicker-images/timepicker-selected-large.png#lightbox "période sélectionnée")

Condition que le [ `Switch` ](xref:Xamarin.Forms.Switch) est activé ou désactivé sur la position, l’application affiche une boîte de dialogue Alerte rappelant de l’utilisateur du texte dans le [ `Entry` ](xref:Xamarin.Forms.Entry) lorsque l’heure sélectionnée se produit :

[![Fenêtre contextuelle du minuteur](timepicker-images/timer-test.png "fenêtre contextuelle du minuteur")](timepicker-images/timer-test-large.png#lightbox "fenêtre contextuelle du minuteur")

Dès que la boîte de dialogue alerte s’affiche, le [ `Switch` ](xref:Xamarin.Forms.Switch) est activé ou désactivé à la position off.

## <a name="related-links"></a>Liens connexes

- [Exemple de SetTimer](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-timepicker)
- [TimePicker API](xref:Xamarin.Forms.TimePicker)
