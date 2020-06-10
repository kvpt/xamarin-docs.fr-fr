---
titre : « Xamarin.Forms timepicker » Description : «timepicker est une Xamarin.Forms vue qui permet à l’utilisateur de sélectionner une heure. Cet article explique comment consommer un TimePicker dans une Xamarin.Forms application.
ms. Prod : xamarin ms. AssetID : 2E99FB23-B82D-4EB4-AFB3-5002E736E7B2 ms. Technology : xamarin-Forms Author : davidbritch ms. Author : dabritch ms. Date : 10/16/2018 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-timepicker"></a>Xamarin.FormsTimePicker

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-timepicker)

_Xamarin.FormsVue qui permet à l’utilisateur de sélectionner une heure._

Xamarin.Forms [`TimePicker`](xref:Xamarin.Forms.TimePicker) Appelle le contrôle de sélecteur de temps de la plateforme et permet à l’utilisateur de sélectionner une heure. `TimePicker` définit les propriétés suivantes :

- [`Time`](xref:Xamarin.Forms.TimePicker.Time)de type `TimeSpan` , l’heure sélectionnée, dont la valeur par défaut est `TimeSpan` égale à 0. Le `TimeSpan` type indique une durée écoulée depuis minuit.
- [`Format`](xref:Xamarin.Forms.TimePicker.Format)de type `string` , une chaîne de mise en forme .NET [standard](/dotnet/standard/base-types/standard-date-and-time-format-strings/) ou [personnalisée](/dotnet/standard/base-types/custom-date-and-time-format-strings/) , qui a comme valeur par défaut « t », le modèle d’heure abrégée.
- [`TextColor`](xref:Xamarin.Forms.TimePicker.TextColor)de type [`Color`](xref:Xamarin.Forms.Color) , la couleur utilisée pour afficher l’heure sélectionnée, qui a comme valeur par défaut [`Color.Default`](xref:Xamarin.Forms.Color.Default) .
- [`FontAttributes`](xref:Xamarin.Forms.TimePicker.FontAttributes)de type [`FontAttributes`](xref:Xamarin.Forms.FontAttributes) , qui a comme valeur par défaut [`FontAtributes.None`](xref:Xamarin.Forms.FontAttributes.None) .
- [`FontFamily`](xref:Xamarin.Forms.TimePicker.FontFamily)de type `string` , qui a comme valeur par défaut `null` .
- [`FontSize`](xref:Xamarin.Forms.TimePicker.FontSize)de type `double` , qui a comme valeur par défaut-1,0.
- `CharacterSpacing`, de type `double` , est l’espacement entre les caractères du `TimePicker` texte.

Toutes ces propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie qu’elles peuvent être stylisées et que les propriétés peuvent être des cibles de liaisons de données. La [`Time`](xref:Xamarin.Forms.TimePicker.Time) propriété a un mode de liaison par défaut de, ce qui [`BindingMode.TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) signifie qu’elle peut être la cible d’une liaison de données dans une application qui utilise l’architecture [MVVM (Model-View-ViewModel)](~/xamarin-forms/enterprise-application-patterns/mvvm.md) .

Le [`TimePicker`](xref:Xamarin.Forms.TimePicker) n’inclut pas d’événement pour indiquer une nouvelle [`Time`](xref:Xamarin.Forms.TimePicker.Time) valeur sélectionnée. Si vous avez besoin d’être informé de cela, vous pouvez ajouter un gestionnaire pour l' [`PropertyChanged`](xref:Xamarin.Forms.BindableObject.PropertyChanged) événement.

## <a name="initializing-the-time-property"></a>Initialisation de la propriété Time

Dans le code, vous pouvez initialiser la [`Time`](xref:Xamarin.Forms.TimePicker.Time) propriété avec une valeur de type `TimeSpan` :

```csharp
TimePicker timePicker = new TimePicker
{
  Time = new TimeSpan(4, 15, 26) // Time set to "04:15:26"
};
```

Lorsque la [`Time`](xref:Xamarin.Forms.TimePicker.Time) propriété est spécifiée en XAML, la valeur est convertie en `TimeSpan` et validée pour garantir que le nombre de millisecondes est supérieur ou égal à 0, et que le nombre d’heures est inférieur à 24. Les composants temporels doivent être séparés par le signe deux-points :

```xaml
<TimePicker Time="4:15:26" />
```

Si la [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) propriété de [`TimePicker`](xref:Xamarin.Forms.TimePicker) a la valeur d’une instance d’un ViewModel contenant une propriété de type `TimeSpan` nommée `SelectedTime` (par exemple), vous pouvez instancier le `TimePicker` comme suit :

```xaml
<TimePicker Time="{Binding SelectedTime}" />
```

Dans cet exemple, la [`Time`](xref:Xamarin.Forms.TimePicker.Time) propriété est initialisée à la `SelectedTime` propriété dans le ViewModel. Étant donné que la `Time` propriété a le mode de liaison [`TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) , toute nouvelle heure sélectionnée par l’utilisateur est automatiquement propagée vers le ViewModel.

Si [`TimePicker`](xref:Xamarin.Forms.TimePicker) ne contient pas de liaison sur sa [`Time`](xref:Xamarin.Forms.TimePicker.Time) propriété, une application doit joindre un gestionnaire à l' [`PropertyChanged`](xref:Xamarin.Forms.BindableObject.PropertyChanged) événement pour être informé lorsque l’utilisateur sélectionne une nouvelle heure.

Pour plus d’informations sur la définition des propriétés de police, consultez [polices](~/xamarin-forms/user-interface/text/fonts.md).

## <a name="timepicker-and-layout"></a>TimePicker et disposition

Il est possible d’utiliser une option de disposition horizontale sans contrainte, telle que `Center` , `Start` ou `End` avec [`TimePicker`](xref:Xamarin.Forms.TimePicker) :

```xaml
<TimePicker ···
            HorizontalOptions="Center"
            ··· />
```

Toutefois, cela n’est pas recommandé. Selon le paramètre de la [`Format`](xref:Xamarin.Forms.TimePicker.Format) propriété, les heures sélectionnées peuvent nécessiter des largeurs d’affichage différentes. Par exemple, la chaîne de format "T" fait [`TimePicker`](xref:Xamarin.Forms.TimePicker) apparaître les heures dans un format long et "4:15:26 AM" nécessite une largeur d’affichage supérieure au format d’heure abrégée ("T") de "4:15 AM". Selon la plateforme, cette différence peut entraîner la modification de la `TimePicker` largeur de la vue dans la disposition ou la troncation de l’affichage.

> [!TIP]
> Il est préférable d’utiliser le `HorizontalOptions` paramètre par défaut `Fill` avec [`TimePicker`](xref:Xamarin.Forms.TimePicker) et non pour utiliser une largeur de lors de la `Auto` mise en place d' `TimePicker` une `Grid` cellule.

## <a name="timepicker-in-an-application"></a>TimePicker dans une application

L’exemple [**SetTimer**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-timepicker) comprend [`TimePicker`](xref:Xamarin.Forms.TimePicker) [`Entry`](xref:Xamarin.Forms.Entry) des vues, et [`Switch`](xref:Xamarin.Forms.Switch) sur sa page. Le `TimePicker` peut être utilisé pour sélectionner une heure et, lorsque ce délai se produit, une boîte de dialogue d’alerte s’affiche pour rappeler l’utilisateur du texte dans le `Entry` , à condition que le `Switch` soit activé. Voici le fichier XAML :

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

[`Entry`](xref:Xamarin.Forms.Entry)Vous permet d’entrer le texte de rappel qui s’affiche lorsque l’heure sélectionnée se produit. [`TimePicker`](xref:Xamarin.Forms.TimePicker)Une [`Format`](xref:Xamarin.Forms.TimePicker.Format) propriété de type « T » est assignée au format d’heure longue. Il possède un gestionnaire d’événements attaché à l' [`PropertyChanged`](xref:Xamarin.Forms.BindableObject.PropertyChanged) événement, et le [`Switch`](xref:Xamarin.Forms.Switch) possède un gestionnaire joint à son [`Toggled`](xref:Xamarin.Forms.Switch.Toggled) événement. Ces gestionnaires d’événements se trouvent dans le fichier code-behind et appellent la `SetTriggerTime` méthode :

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

La `SetTriggerTime` méthode calcule le temps d’une minuterie en fonction de la valeur de la `DateTime.Today` propriété et de la `TimeSpan` valeur retournée par [`TimePicker`](xref:Xamarin.Forms.TimePicker) . Cela est nécessaire, car la `DateTime.Today` propriété retourne une valeur `DateTime` indiquant la date actuelle, mais avec une heure de minuit. Si le temps du minuteur est déjà passé aujourd’hui, il est supposé être demain.

La minuterie s’exécute chaque seconde, en exécutant la `OnTimerTick` méthode qui vérifie si le [`Switch`](xref:Xamarin.Forms.Switch) est activé et si l’heure actuelle est supérieure ou égale au temps de la minuterie. Lorsque le temps de la minuterie se produit, la [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) méthode présente une boîte de dialogue d’alerte à l’utilisateur sous forme de rappel.

Lorsque l’exemple est exécuté pour la première fois, la [`TimePicker`](xref:Xamarin.Forms.TimePicker) vue est initialisée sur 11:00. En appuyant sur `TimePicker` , vous appelez le sélecteur d’heure de la plateforme. Les plateformes implémentent le sélecteur d’heure de manière très différente, mais chaque approche est familière aux utilisateurs de cette plateforme :

[![Sélectionner l’heure](timepicker-images/timepicker-open.png "Sélectionner l’heure")](timepicker-images/timepicker-open-large.png#lightbox "Sélectionner l’heure")

> [!TIP]
> Sur Android, la [`TimePicker`](xref:Xamarin.Forms.TimePicker) boîte de dialogue peut être personnalisée en substituant la `CreateTimePickerDialog` méthode dans un convertisseur personnalisé. Cela permet, par exemple, l’ajout de boutons supplémentaires à la boîte de dialogue.

Après avoir sélectionné une heure, l’heure sélectionnée s’affiche dans le [`TimePicker`](xref:Xamarin.Forms.TimePicker) :

[![Heure sélectionnée](timepicker-images/timepicker-selected.png "Heure sélectionnée")](timepicker-images/timepicker-selected-large.png#lightbox "Heure sélectionnée")

Si [`Switch`](xref:Xamarin.Forms.Switch) est basculé vers la position on, l’application affiche une boîte de dialogue d’alerte rappelant l’utilisateur du texte dans le [`Entry`](xref:Xamarin.Forms.Entry) lorsque l’heure sélectionnée se produit :

[![Fenêtre contextuelle du minuteur](timepicker-images/timer-test.png "Fenêtre contextuelle du minuteur")](timepicker-images/timer-test-large.png#lightbox "Fenêtre contextuelle du minuteur")

Dès que la boîte de dialogue d’alerte s’affiche, la [`Switch`](xref:Xamarin.Forms.Switch) est basculée à la position OFF.

## <a name="related-links"></a>Liens connexes

- [Exemple SetTimer](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-timepicker)
- [API TimePicker](xref:Xamarin.Forms.TimePicker)
