---
title: Sélecteur d’heure
description: Sélection d’une heure à l’aide de TimePickerDialog et DialogFragment
ms.prod: xamarin
ms.assetid: EB4E8206-E8AD-9F04-AC1C-82AC9364A9DD
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: e959f0d3cafa9e856458472668244fcd33ba4f81
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68510232"
---
# <a name="android-time-picker"></a>Sélecteur d’heure Android

Pour permettre à l’utilisateur de sélectionner une heure, vous pouvez utiliser [timepicker](xref:Android.Widget.TimePicker). En général, les `TimePicker` applications Android utilisent [TimePickerDialog](xref:Android.App.TimePickerDialog) pour sélectionner une &ndash; valeur temporelle. cela permet de garantir une interface cohérente entre les appareils et les applications. `TimePicker`permet aux utilisateurs de sélectionner l’heure de la journée en mode AM/PM de 24 heures ou 12 heures.
`TimePickerDialog`est une classe d’assistance qui encapsule le `TimePicker` dans une boîte de dialogue.

[![Exemple de capture d’écran de la boîte de dialogue du sélecteur d’heure en action](time-picker-images/01-example-screen-sml.png)](time-picker-images/01-example-screen.png#lightbox)

## <a name="overview"></a>Présentation

Les `TimePickerDialog` applications Android modernes affichent dans un [DialogFragment](xref:Android.App.DialogFragment). Cela permet à une application d’afficher en tant que `TimePicker` boîte de dialogue contextuelle ou de l’incorporer dans une activité. En outre, `DialogFragment` gère le cycle de vie et l’affichage de la boîte de dialogue, réduisant ainsi la quantité de code qui doit être implémentée.

Ce guide montre comment utiliser le `TimePickerDialog`, encapsulé dans un. `DialogFragment` L’exemple d’application affiche `TimePickerDialog` le sous la forme d’une boîte de dialogue modale lorsque l’utilisateur clique sur un bouton d’une activité. Lorsque l’heure est définie par l’utilisateur, la boîte de dialogue se ferme et un gestionnaire met à `TextView` jour un sur l’écran de l’activité avec l’heure qui a été sélectionnée.

## <a name="requirements"></a>Configuration requise

L’exemple d’application pour ce guide cible Android 4,1 (niveau d’API
16) ou version ultérieure, mais peut être utilisé avec Android 3,0 (niveau d’API 11 ou supérieur). Il est possible de prendre en charge des versions antérieures d’Android avec l’ajout de la bibliothèque de prise en charge Android au projet et certaines modifications du code.

## <a name="using-the-timepicker"></a>Utilisation de TimePicker

Cet exemple s' `DialogFragment`étend; l’implémentation de la `DialogFragment` sous- `TimePickerFragment` classe de (appelée ci- `TimePickerDialog`dessous) héberge et affiche un. Lorsque l’exemple d’application est lancé pour la première fois  , il affiche un bouton `TextView` de sélection au-dessus d’un qui sera utilisé pour afficher l’heure sélectionnée:

[![Écran d’exemple d’application initial](time-picker-images/02-initial-app-screen-sml.png)](time-picker-images/02-initial-app-screen.png#lightbox)

Lorsque vous cliquez sur le bouton **choisir une heure** , l’exemple d' `TimePickerDialog` application lance la, comme illustré dans cette capture d’écran:

[![Capture d’écran de la boîte de dialogue du sélecteur d’heure par défaut affichée par l’application](time-picker-images/03-am-pm-time-dialog-sml.png)](time-picker-images/03-am-pm-time-dialog.png#lightbox)

Dans le `TimePickerDialog`, si vous sélectionnez une heure et  cliquez sur le bouton `TimePickerDialog` OK, le appelle la méthode [IOnTimeSetListener. OnTimeSet](xref:Android.App.TimePickerDialog.IOnTimeSetListener.OnTimeSet*).
Cette interface est implémentée par l' `DialogFragment` hébergement`TimePickerFragment`(, décrit ci-dessous). Lorsque vous cliquez sur le bouton **Annuler** , le fragment et la boîte de dialogue sont ignorés.

`DialogFragment`retourne l’heure sélectionnée à l’activité d’hébergement de l’une des trois façons suivantes:

1. **Appel d’une méthode ou définition d’une propriété** &ndash; L’activité peut fournir une propriété ou une méthode spécifiquement pour la définition de cette valeur.

2. **Déclenchement d’un événement** Peut définir un événement qui sera déclenché quand `OnTimeSet` est appelé. &ndash; `DialogFragment`

3. L' **utilisation `Action` d’un** &ndash; objet peut appeler un `Action<DateTime>` pour afficher l’heure dans l’activité. `DialogFragment` L’activité fournira `Action<DateTime` lors de l’instanciation de `DialogFragment`.

Cet exemple utilise la troisième technique, qui requiert que l’activité fournisse un `Action<DateTime>` gestionnaire `DialogFragment`à.

## <a name="start-an-app-project"></a>Démarrer un projet d’application

Démarrez un nouveau projet Android appelé **TimePickerDemo** (si vous n’êtes pas familiarisé avec la création de projets Xamarin. Android, consultez [Hello, Android](~/android/get-started/hello-android/hello-android-quickstart.md) pour apprendre à créer un nouveau projet).

Modifiez **Resources/layout/main. AXML** et remplacez son contenu par le code XML suivant:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:layout_gravity="center_horizontal"
    android:padding="16dp">
    <Button
        android:id="@+id/select_button"
        android:paddingLeft="24dp"
        android:paddingRight="24dp"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="PICK TIME"
        android:textSize="20dp" />
    <TextView
        android:id="@+id/time_display"
        android:layout_height="wrap_content"
        android:layout_width="match_parent"
        android:paddingTop="22dp"
        android:text="Picked time will be displayed here"
        android:textSize="24dp" />
</LinearLayout>
```

Il s’agit d’un [élément LinearLayout](xref:Android.Widget.LinearLayout) de base avec un [TextView](xref:Android.Widget.TextView) qui affiche l’heure et un [bouton](xref:Android.Widget.Button) qui ouvre le `TimePickerDialog`. Notez que cette disposition utilise des chaînes codées en dur et des dimensions pour rendre l’application plus simple et &ndash; plus facile à comprendre une application de production utilise normalement des ressources pour ces valeurs (comme vous pouvez le voir dans l’exemple de code [DatePicker](https://github.com/xamarin/recipes/blob/master/Recipes/android/controls/datepicker/select_a_date/Resources/layout/Main.axml) ).

Modifiez **MainActivity.cs** et remplacez son contenu par le code suivant:

```csharp
using Android.App;
using Android.Widget;
using Android.OS;
using System;
using Android.Util;
using Android.Text.Format;

namespace TimePickerDemo
{
    [Activity(Label = "TimePickerDemo", MainLauncher = true, Icon = "@drawable/icon")]
    public class MainActivity : Activity
    {
        TextView timeDisplay;
        Button timeSelectButton;

        protected override void OnCreate(Bundle bundle)
        {
            base.OnCreate(bundle);
            SetContentView(Resource.Layout.Main);
            timeDisplay = FindViewById<TextView>(Resource.Id.time_display);
            timeSelectButton = FindViewById<Button>(Resource.Id.select_button);
        }
    }
}
```

Lorsque vous générez et exécutez cet exemple, vous devez voir un écran initial similaire à la capture d’écran suivante:

[![Écran d’application initial](time-picker-images/02-initial-app-screen-sml.png)](time-picker-images/02-initial-app-screen.png#lightbox)

Le fait de cliquer sur le bouton **choisir** un `DialogFragment` moment n’a aucun effet, car le `TimePicker`n’a pas encore été implémenté pour afficher.
L’étape suivante consiste à créer cette `DialogFragment`.

## <a name="extending-dialogfragment"></a>Extension de DialogFragment

Pour étendre `DialogFragment` en vue de `TimePicker`son utilisation avec, il est nécessaire de créer une sous-classe `DialogFragment` `TimePickerDialog.IOnTimeSetListener`dérivée de et implémente. Ajoutez la classe suivante à **MainActivity.cs**:

```csharp
public class TimePickerFragment : DialogFragment, TimePickerDialog.IOnTimeSetListener
{
    public static readonly string TAG = "MyTimePickerFragment";
    Action<DateTime> timeSelectedHandler = delegate { };

    public static TimePickerFragment NewInstance(Action<DateTime> onTimeSelected)
    {
        TimePickerFragment frag = new TimePickerFragment();
        frag.timeSelectedHandler = onTimeSelected;
        return frag;
    }

    public override Dialog OnCreateDialog (Bundle savedInstanceState)
    {
        DateTime currentTime = DateTime.Now;
        bool is24HourFormat = DateFormat.Is24HourFormat(Activity);
        TimePickerDialog dialog = new TimePickerDialog
            (Activity, this, currentTime.Hour, currentTime.Minute, is24HourFormat);
        return dialog;
    }

    public void OnTimeSet(TimePicker view, int hourOfDay, int minute)
    {
        DateTime currentTime = DateTime.Now;
        DateTime selectedTime = new DateTime(currentTime.Year, currentTime.Month, currentTime.Day, hourOfDay, minute, 0);
        Log.Debug(TAG, selectedTime.ToLongTimeString());
        timeSelectedHandler (selectedTime);
    }
}
```

Cette `TimePickerFragment` classe est divisée en éléments plus petits et expliquée dans la section suivante.

### <a name="dialogfragment-implementation"></a>Implémentation de DialogFragment

`TimePickerFragment`implémente plusieurs méthodes: une méthode de fabrique, une méthode d’instanciation de `OnTimeSet` boîte de dialogue et `TimePickerDialog.IOnTimeSetListener`la méthode de gestionnaire requise par.

-   `TimePickerFragment`est une sous-classe `DialogFragment`de. Il implémente également l' `TimePickerDialog.IOnTimeSetListener` interface (autrement dit, il fournit la méthode `OnTimeSet` requise):

    ```csharp
    public class TimePickerFragment : DialogFragment, TimePickerDialog.IOnTimeSetListener
    ```

-   `TAG`est initialisé à des fins de journalisation (*MyTimePickerFragment* peut être remplacé par la chaîne que vous souhaitez utiliser). L' `timeSelectedHandler` action est initialisée sur un délégué vide pour empêcher les exceptions de référence null:

    ```csharp
    public static readonly string TAG = "MyTimePickerFragment";
    Action<DateTime> timeSelectedHandler = delegate { };
    ```

-   La `NewInstance` méthode de fabrique est appelée pour instancier `TimePickerFragment`un nouveau. Cette méthode prend un `Action<DateTime>` gestionnaire qui est appelé quand l’utilisateur clique sur le bouton **OK** dans `TimePickerDialog`le:

    ```csharp
    public static TimePickerFragment NewInstance(Action<DateTime> onTimeSelected)
    {
        TimePickerFragment frag = new TimePickerFragment();
        frag.timeSelectedHandler = onTimeSelected;
        return frag;
    }
    ```

-   Lorsque le fragment doit être affiché, Android appelle la `DialogFragment` méthode [OnCreateDialog](xref:Android.App.DialogFragment.OnCreateDialog*).
    Cette méthode crée un nouvel `TimePickerDialog` objet et l’initialise avec l’activité, l’objet `TimePickerFragment`de rappel (qui est l’instance actuelle de) et l’heure actuelle:

    ```csharp
    public override Dialog OnCreateDialog (Bundle savedInstanceState)
    {
        DateTime currentTime = DateTime.Now;
        bool is24HourFormat = DateFormat.Is24HourFormat(Activity);
        TimePickerDialog dialog = new TimePickerDialog
            (Activity, this, currentTime.Hour, currentTime.Minute, is24HourFormat);
        return dialog;
    }
    ```

-   Lorsque l’utilisateur modifie le paramètre d’heure dans `TimePicker` la boîte de `OnTimeSet` dialogue, la méthode est appelée. `OnTimeSet`crée un `DateTime` objet à l’aide de la date actuelle et des fusions dans l’heure (heure et minute) sélectionnées par l’utilisateur:

    ```csharp
    public void OnTimeSet(TimePicker view, int hourOfDay, int minute)
    {
        DateTime currentTime = DateTime.Now;
        DateTime selectedTime = new DateTime(currentTime.Year, currentTime.Month, currentTime.Day, hourOfDay, minute, 0);
    ```


-   Cet `DateTime` objet est passé `timeSelectedHandler` au qui est inscrit avec l' `TimePickerFragment` objet au moment de la création. `OnTimeSet`appelle ce gestionnaire pour mettre à jour l’affichage de l’heure de l’activité à l’heure sélectionnée (ce gestionnaire est implémenté dans la section suivante):

    ```csharp
    timeSelectedHandler (selectedTime);
    ```

## <a name="displaying-the-timepickerfragment"></a>Affichage du TimePickerFragment

Maintenant que le `DialogFragment` a été implémenté, il est temps d’instancier `DialogFragment` le à `NewInstance` l’aide de la méthode de fabrique et de l’afficher en appelant [DialogFragment. Show](xref:Android.App.DialogFragment.Show*):

Ajoutez la méthode suivante à `MainActivity`:

```csharp
void TimeSelectOnClick (object sender, EventArgs eventArgs)
{
    TimePickerFragment frag = TimePickerFragment.NewInstance (
        delegate (DateTime time)
        {
            timeDisplay.Text = time.ToShortTimeString();
        });

    frag.Show(FragmentManager, TimePickerFragment.TAG);
}
```

Après `TimeSelectOnClick` instanciation d' `TimePickerFragment`un, il crée et passe dans un délégué pour une méthode anonyme qui met à jour l’affichage de l’heure de l’activité avec la valeur d’heure passée. Enfin, il lance le `TimePicker` fragment de boîte de `DialogFragment.Show`dialogue (via) `TimePicker` pour afficher l’à l’utilisateur.

À la fin de la `OnCreate` méthode, ajoutez la ligne suivante pour attacher le gestionnaire d’événements au bouton d' **heure de sélection** qui lance la boîte de dialogue:

```csharp
timeSelectButton.Click += TimeSelectOnClick;
```

Lorsque vous cliquez sur le bouton **choisir** une heure `TimeSelectOnClick` , est appelé pour afficher le `TimePicker` fragment de boîte de dialogue à l’utilisateur.

## <a name="try-it"></a>Essayez !

Générez et exécutez l’application. Lorsque vous cliquez sur le bouton **choisir** une heure `TimePickerDialog` , le s’affiche dans le format d’heure par défaut de l’activité (dans ce cas, le mode AM/PM de 12 heures):

[![La boîte de dialogue heure s’affiche en mode AM/PM.](time-picker-images/03-am-pm-time-dialog-sml.png)](time-picker-images/03-am-pm-time-dialog.png#lightbox)
   
Lorsque vous cliquez sur **OK** dans `TimePicker` la boîte de dialogue, le gestionnaire met à jour `TextView` l’activité de l’activité avec l’heure choisie, puis se ferme:

[![L’heure a/M est affichée dans le TextView de l’activité](time-picker-images/04-after-time-dialog-sml.png)](time-picker-images/04-after-time-dialog.png#lightbox)

Ensuite, ajoutez la ligne de code suivante à `OnCreateDialog` juste après `is24HourFormat` la déclaration et l’initialisation de.

```csharp
is24HourFormat = true;
```

Cette modification force l’indicateur passé au `TimePickerDialog` constructeur à être `true` afin que le mode 24 heures soit utilisé à la place du format d’heure de l’activité d’hébergement. Lorsque vous générez et exécutez à nouveau l’application, cliquez sur le bouton choisir `TimePicker` une **heure** , la boîte de dialogue s’affiche maintenant au format 24 heures:

[![Boîte de dialogue TimePicker au format 24 heures](time-picker-images/05-24hr-time-dialog-sml.png)](time-picker-images/05-24hr-time-dialog.png#lightbox)

Étant donné que le gestionnaire appelle [DateTime. ToShortTimeString](xref:System.DateTime.ToShortDateString*) pour imprimer l’heure dans l' `TextView`activité, l’heure est toujours imprimée dans le format par défaut de 12 heures AM/PM.

## <a name="summary"></a>Récapitulatif

Cet article a expliqué comment afficher un `TimePicker` widget en tant que boîte de dialogue modale à partir d’une activité Android. Il a fourni un `DialogFragment` exemple d’implémentation et `IOnTimeSetListener` a abordé l’interface. Cet exemple montre également comment le `DialogFragment` peut interagir avec l’activité de l’hôte pour afficher l’heure sélectionnée.

## <a name="related-links"></a>Liens associés

- [DialogFragment](xref:Android.App.DialogFragment)
- [Sélecteur d’heure](xref:Android.Widget.TimePicker)
- [TimePickerDialog](xref:Android.App.TimePickerDialog)
- [TimePickerDialog.IOnTimeSetListener](xref:Android.App.TimePickerDialog.IOnTimeSetListener)
- [TimePickerDemo (exemple)](https://developer.xamarin.com/samples/monodroid/UserInterface/TimePickerDemo)
