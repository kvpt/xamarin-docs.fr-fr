---
title: Xamarin. Forms TimePicker
description: TimePicker est un affichage Xamarin. Forms qui permet à l’utilisateur de sélectionner une heure. Cet article explique comment consommer un TimePicker dans une application Xamarin. Forms.
ms.prod: xamarin
ms.assetid: 2E99FB23-B82D-4EB4-AFB3-5002E736E7B2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/16/2018
ms.openlocfilehash: aae0791199b0e3042a3c619fcb11e7b877f52012
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72695920"
---
# <a name="xamarinforms-timepicker"></a>Xamarin. Forms TimePicker

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-timepicker)

_Vue Xamarin. Forms qui permet à l’utilisateur de sélectionner une heure._

Xamarin. Forms [`TimePicker`](xref:Xamarin.Forms.TimePicker) appelle le contrôle Time-Picker de la plateforme et permet à l’utilisateur de sélectionner une heure. `TimePicker` définit les propriétés suivantes :

- [`Time`](xref:Xamarin.Forms.TimePicker.Time) de type `TimeSpan`, heure sélectionnée, dont la valeur par défaut est un `TimeSpan` de 0. Le type de `TimeSpan` indique une durée écoulée depuis minuit.
- [`Format`](xref:Xamarin.Forms.TimePicker.Format) de type `string`, une chaîne de mise en forme .NET [standard](/dotnet/standard/base-types/standard-date-and-time-format-strings/) ou [personnalisée](/dotnet/standard/base-types/custom-date-and-time-format-strings/) , qui a comme valeur par défaut « t », le modèle d’heure abrégée.
- [`TextColor`](xref:Xamarin.Forms.TimePicker.TextColor) de type [`Color`](xref:Xamarin.Forms.Color), la couleur utilisée pour afficher l’heure sélectionnée, qui est [`Color.Default`](xref:Xamarin.Forms.Color.Default)par défaut.
- [`FontAttributes`](xref:Xamarin.Forms.TimePicker.FontAttributes) de type [`FontAttributes`](xref:Xamarin.Forms.FontAttributes), qui a par défaut la valeur [`FontAtributes.None`](xref:Xamarin.Forms.FontAttributes.None).
- [`FontFamily`](xref:Xamarin.Forms.TimePicker.FontFamily) de type `string`, qui a par défaut la valeur `null`.
- [`FontSize`](xref:Xamarin.Forms.TimePicker.FontSize) de type `double`, dont la valeur par défaut est-1,0.
- `CharacterSpacing`, de type `double`, est l’espacement entre les caractères du texte de `TimePicker`.

Toutes ces propriétés sont sauvegardées par des objets [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , ce qui signifie qu’ils peuvent être stylisés, et les propriétés peuvent être des cibles de liaisons de données. La propriété [`Time`](xref:Xamarin.Forms.TimePicker.Time) a un mode de liaison par défaut de [`BindingMode.TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay), ce qui signifie qu’elle peut être la cible d’une liaison de données dans une application qui utilise l’architecture [MVVM (Model-View-ViewModel)](~/xamarin-forms/enterprise-application-patterns/mvvm.md) .

La [`TimePicker`](xref:Xamarin.Forms.TimePicker) n’inclut pas d’événement pour indiquer une nouvelle valeur de [`Time`](xref:Xamarin.Forms.TimePicker.Time) sélectionnée. Si vous avez besoin d’être informé de cela, vous pouvez ajouter un gestionnaire pour l’événement [`PropertyChanged`](xref:Xamarin.Forms.BindableObject.PropertyChanged) .

## <a name="initializing-the-time-property"></a>Initialisation de la propriété Time

Dans le code, vous pouvez initialiser la propriété [`Time`](xref:Xamarin.Forms.TimePicker.Time) avec une valeur de type `TimeSpan` :

```csharp
TimePicker timePicker = new TimePicker
{
  Time = new TimeSpan(4, 15, 26) // Time set to "04:15:26"
};
```

Quand la propriété [`Time`](xref:Xamarin.Forms.TimePicker.Time) est spécifiée en XAML, la valeur est convertie en `TimeSpan` et validée pour garantir que le nombre de millisecondes est supérieur ou égal à 0, et que le nombre d’heures est inférieur à 24. Les composants temporels doivent être séparés par le signe deux-points :

```xaml
<TimePicker Time="4:15:26" />
```

Si la propriété [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de [`TimePicker`](xref:Xamarin.Forms.TimePicker) est définie sur une instance d’un ViewModel contenant une propriété de type `TimeSpan` nommée `SelectedTime` (par exemple), vous pouvez instancier le `TimePicker` comme suit :

```xaml
<TimePicker Time="{Binding SelectedTime}" />
```

Dans cet exemple, la propriété [`Time`](xref:Xamarin.Forms.TimePicker.Time) est initialisée sur la propriété `SelectedTime` dans le ViewModel. Étant donné que la propriété `Time` a le mode de liaison [`TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay), toute nouvelle heure sélectionnée par l’utilisateur est automatiquement propagée vers le ViewModel.

Si le [`TimePicker`](xref:Xamarin.Forms.TimePicker) ne contient pas de liaison sur sa propriété [`Time`](xref:Xamarin.Forms.TimePicker.Time) , une application doit joindre un gestionnaire à l’événement [`PropertyChanged`](xref:Xamarin.Forms.BindableObject.PropertyChanged) pour être informé lorsque l’utilisateur sélectionne une nouvelle heure.

Pour plus d’informations sur la définition des propriétés de police, consultez [polices](~/xamarin-forms/user-interface/text/fonts.md).

## <a name="timepicker-and-layout"></a>TimePicker et disposition

Il est possible d’utiliser une option de disposition horizontale sans contrainte comme `Center`, `Start` ou `End` avec [`TimePicker`](xref:Xamarin.Forms.TimePicker):

```xaml
<TimePicker ···
            HorizontalOptions="Center"
            ··· />
```

Toutefois, cela n’est pas recommandé. Selon le paramètre de la propriété [`Format`](xref:Xamarin.Forms.TimePicker.Format) , les heures sélectionnées peuvent nécessiter des largeurs d’affichage différentes. Par exemple, la chaîne de format "T" provoque l’affichage des heures dans un format long par la vue de [`TimePicker`](xref:Xamarin.Forms.TimePicker) , et "4:15:26 AM" nécessite une largeur d’affichage supérieure au format d’heure abrégée ("T") de "4:15 AM". En fonction de la plateforme, cette différence peut entraîner la modification de la largeur de la vue `TimePicker` dans la disposition ou la troncation de l’affichage.

> [!TIP]
> Il est préférable d’utiliser le paramètre par défaut `HorizontalOptions` de `Fill` avec [`TimePicker`](xref:Xamarin.Forms.TimePicker), et de ne pas utiliser une largeur de `Auto` lors de l’ajout de `TimePicker` dans une cellule `Grid`.

## <a name="timepicker-in-an-application"></a>TimePicker dans une application

L’exemple [**SetTimer**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-timepicker) comprend des vues [`TimePicker`](xref:Xamarin.Forms.TimePicker), [`Entry`](xref:Xamarin.Forms.Entry)et [`Switch`](xref:Xamarin.Forms.Switch) sur sa page. La `TimePicker` peut être utilisée pour sélectionner une heure et, lorsque ce délai se produit, une boîte de dialogue d’alerte s’affiche pour rappeler l’utilisateur du texte du `Entry`, à condition que le `Switch` soit activé. Voici le fichier XAML :

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

La [`Entry`](xref:Xamarin.Forms.Entry) vous permet d’entrer le texte de rappel qui s’affiche lorsque l’heure sélectionnée se produit. La [`TimePicker`](xref:Xamarin.Forms.TimePicker) est assignée à la propriété [`Format`](xref:Xamarin.Forms.TimePicker.Format) de « t » pour le format d’heure longue. Il possède un gestionnaire d’événements attaché à l’événement [`PropertyChanged`](xref:Xamarin.Forms.BindableObject.PropertyChanged) , et le [`Switch`](xref:Xamarin.Forms.Switch) possède un gestionnaire attaché à son événement [`Toggled`](xref:Xamarin.Forms.Switch.Toggled) . Ces gestionnaires d’événements se trouvent dans le fichier code-behind et appellent la méthode `SetTriggerTime` :

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

La méthode `SetTriggerTime` calcule le temps d’une minuterie en fonction de la valeur de la propriété `DateTime.Today` et de la valeur `TimeSpan` retournée par la [`TimePicker`](xref:Xamarin.Forms.TimePicker). Cela est nécessaire, car la propriété `DateTime.Today` retourne un `DateTime` indiquant la date actuelle, mais avec une heure de minuit. Si le temps du minuteur est déjà passé aujourd’hui, il est supposé être demain.

La minuterie s’exécute chaque seconde, en exécutant la méthode `OnTimerTick` qui vérifie si la [`Switch`](xref:Xamarin.Forms.Switch) est activée et si l’heure actuelle est supérieure ou égale au temps de la minuterie. Lorsque le temps de la minuterie se produit, la méthode [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) présente une boîte de dialogue d’alerte à l’utilisateur sous forme de rappel.

Lorsque l’exemple est exécuté pour la première fois, la vue [`TimePicker`](xref:Xamarin.Forms.TimePicker) est initialisée sur 11:00. Appuyez sur le `TimePicker` appelle le sélecteur d’heure de la plateforme. Les plateformes implémentent le sélecteur d’heure de manière très différente, mais chaque approche est familière aux utilisateurs de cette plateforme :

[![Sélectionner l’heure](timepicker-images/timepicker-open.png "Sélectionner l’heure")](timepicker-images/timepicker-open-large.png#lightbox "Sélectionner l’heure")

> [!TIP]
> Sur Android, la boîte de dialogue [`TimePicker`](xref:Xamarin.Forms.TimePicker) peut être personnalisée en remplaçant la méthode `CreateTimePickerDialog` dans un convertisseur personnalisé. Cela permet, par exemple, l’ajout de boutons supplémentaires à la boîte de dialogue.

Après avoir sélectionné une heure, l’heure sélectionnée s’affiche dans le [`TimePicker`](xref:Xamarin.Forms.TimePicker):

[![Heure sélectionnée](timepicker-images/timepicker-selected.png "Heure sélectionnée")](timepicker-images/timepicker-selected-large.png#lightbox "Heure sélectionnée")

Si le [`Switch`](xref:Xamarin.Forms.Switch) est basculé vers la position on, l’application affiche une boîte de dialogue d’alerte rappelant l’utilisateur du texte dans le [`Entry`](xref:Xamarin.Forms.Entry) lorsque l’heure sélectionnée se produit :

[![Fenêtre contextuelle du minuteur](timepicker-images/timer-test.png "Fenêtre contextuelle du minuteur")](timepicker-images/timer-test-large.png#lightbox "Fenêtre contextuelle du minuteur")

Dès que la boîte de dialogue d’alerte s’affiche, le [`Switch`](xref:Xamarin.Forms.Switch) est basculé vers la position OFF.

## <a name="related-links"></a>Liens connexes

- [Exemple SetTimer](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-timepicker)
- [API TimePicker](xref:Xamarin.Forms.TimePicker)
