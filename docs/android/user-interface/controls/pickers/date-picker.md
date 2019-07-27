---
title: Sélecteur de dates
description: Sélection de dates de calendrier à l’aide des DatePickerDialog et DialogFragment
ms.prod: xamarin
ms.assetid: F2BCD8D4-8957-EA53-C5A8-6BB603ADB47B
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 01/22/2018
ms.openlocfilehash: ef9abbd60fc83622631b916c50f4993c1c848b00
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68510253"
---
# <a name="android-date-picker"></a>Sélecteur de dates Android

## <a name="overview"></a>Présentation

Il peut arriver qu’un utilisateur doive entrer des données dans une application Android. Pour vous aider, l’infrastructure Android fournit le [`DatePicker`](xref:Android.Widget.DatePicker) widget et le. [`DatePickerDialog`](xref:Android.App.DatePickerDialog) Le `DatePicker` permet aux utilisateurs de sélectionner l’année, le mois et le jour dans une interface cohérente entre les appareils et les applications. Est une classe d’assistance qui encapsule le `DatePicker` dans une boîte de dialogue. `DatePickerDialog`

Les applications Android modernes doivent afficher `DatePickerDialog` le dans [`DialogFragment`](xref:Android.App.DialogFragment)un. Cela permettra à une application d’afficher le DatePicker en tant que boîte de dialogue contextuelle ou incorporée dans une activité. En outre, le `DialogFragment` gère le cycle de vie et l’affichage de la boîte de dialogue, réduisant ainsi la quantité de code qui doit être implémentée.

Ce guide montre comment utiliser le `DatePickerDialog`, encapsulé dans un. `DialogFragment` L’exemple d’application affiche le `DatePickerDialog` sous la forme d’une boîte de dialogue modale quand l’utilisateur clique sur un bouton d’une activité. Lorsque la date est définie par l’utilisateur, un `TextView` est mis à jour avec la date sélectionnée.

[![Capture d’écran du bouton de date de sélection suivi de la boîte de dialogue Sélecteur de dates](date-picker-images/image-01-sml.png)](date-picker-images/image-01.png#lightbox)

## <a name="requirements"></a>Configuration requise

L’exemple d’application pour ce guide cible Android 4,1 (niveau d’API
16) ou version ultérieure, mais s’applique à Android 3,0 (niveau d’API 11 ou supérieur). Il est possible de prendre en charge des versions antérieures d’Android avec l’ajout de la bibliothèque de prise en charge Android au projet et certaines modifications du code.

## <a name="using-the-datepicker"></a>Utilisation du DatePicker

Cet exemple s’étend `DialogFragment`. La sous-classe hébergera et affichera `DatePickerDialog`:

![Gros plan de boîte de dialogue Sélecteur de dates](date-picker-images/image-02.png)

Lorsque l’utilisateur sélectionne une date et clique  sur le bouton OK `DatePickerDialog` , le appelle la [`IOnDateSetListener.OnDateSet`](xref:Android.App.DatePickerDialog.IOnDateSetListener.OnDateSet*)méthode.
Cette interface est implémentée par l' `DialogFragment`hébergement. Si l’utilisateur clique sur le bouton **Annuler** , le fragment et la boîte de dialogue se referment.

Il existe plusieurs façons `DialogFragment` de retourner la date sélectionnée à l’activité d’hébergement:

1. **Appeler une méthode ou définir une propriété** &ndash; L’activité peut fournir une propriété ou une méthode spécifiquement pour la définition de cette valeur.

2. **Déclencher un événement** Peut définir un événement qui sera déclenché quand `OnDateSet` est appelé. &ndash; `DialogFragment`

3. **Utilisez un `Action`**  &ndash; qui peut appeler un`Action<DateTime>` pour afficher la date dans l’activité. `DialogFragment` L’activité fournira `Action<DateTime` lors de l’instanciation de `DialogFragment`. Cet exemple utilise la troisième technique et exige que l’activité fournisse un `Action<DateTime>` `DialogFragment`au.

### <a name="extending-dialogfragment"></a>Extension de DialogFragment

La première étape de l’affichage `DatePickerDialog` d’un consiste à `DialogFragment` effectuer une sous-classe `IOnDateSetListener` et à faire en sorte qu’elle implémente l’interface:

```csharp
public class DatePickerFragment : DialogFragment, 
                                  DatePickerDialog.IOnDateSetListener
{
    // TAG can be any string of your choice.
    public static readonly string TAG = "X:" + typeof (DatePickerFragment).Name.ToUpper();
    
    // Initialize this value to prevent NullReferenceExceptions.
    Action<DateTime> _dateSelectedHandler = delegate { };
    
    public static DatePickerFragment NewInstance(Action<DateTime> onDateSelected)
    {
        DatePickerFragment frag = new DatePickerFragment();
        frag._dateSelectedHandler = onDateSelected;
        return frag;
    }
    
    public override Dialog OnCreateDialog(Bundle savedInstanceState)
    {
        DateTime currently = DateTime.Now;
        DatePickerDialog dialog = new DatePickerDialog(Activity, 
                                                       this, 
                                                       currently.Year, 
                                                       currently.Month - 1,
                                                       currently.Day);
        return dialog;
    }
    
    public void OnDateSet(DatePicker view, int year, int monthOfYear, int dayOfMonth)
    {
        // Note: monthOfYear is a value between 0 and 11, not 1 and 12!
        DateTime selectedDate = new DateTime(year, monthOfYear + 1, dayOfMonth);
        Log.Debug(TAG, selectedDate.ToLongDateString());
        _dateSelectedHandler(selectedDate);
    }
}
```

La `NewInstance` méthode est appelée pour instancier un nouveau `DatePickerFragment`. Cette méthode prend un `Action<DateTime>` qui est appelé quand l’utilisateur clique sur le bouton **OK** dans `DatePickerDialog`.

Lorsque le fragment sera affiché, Android appellera la méthode `OnCreateDialog`. Cette méthode crée un nouvel `DatePickerDialog` objet et l’initialise avec la date actuelle et l’objet `DatePickerFragment`de rappel (qui correspond à l’instance actuelle du).

> [!NOTE]
> N’oubliez pas que la valeur du mois quand `IOnDateSetListener.OnDateSet` est appelé est comprise entre 0 et 11, et non pas entre 1 et 12. Le jour du mois se trouve dans la plage de 1 à 31 (selon le mois sélectionné).

### <a name="showing-the-datepickerfragment"></a>Présentation de DatePickerFragment

Maintenant que le `DialogFragment` a été implémenté, cette section examine l’utilisation du fragment dans une activité. Dans l’exemple d’application qui accompagne ce guide, l’activité instancie le `DialogFragment` à l' `NewInstance` aide de la méthode `DialogFragment.Show`de fabrique, puis l’affiche. Dans le cadre de l’instanciation du `DialogFragment`, l’activité passe un `Action<DateTime>`, qui affiche la date dans `TextView` un hébergé par l’activité:

```csharp
[Activity(Label = "@string/app_name", MainLauncher = true, Icon = "@drawable/icon")]
public class MainActivity : Activity
{
    TextView _dateDisplay;
    Button _dateSelectButton;

    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);
        SetContentView(Resource.Layout.Main);

        _dateDisplay = FindViewById<TextView>(Resource.Id.date_display);
        _dateSelectButton = FindViewById<Button>(Resource.Id.date_select_button);
        _dateSelectButton.Click += DateSelect_OnClick;
    }

    void DateSelect_OnClick(object sender, EventArgs eventArgs)
    {
        DatePickerFragment frag = DatePickerFragment.NewInstance(delegate(DateTime time)
                                                                 {
                                                                     _dateDisplay.Text = time.ToLongDateString();
                                                                 });
        frag.Show(FragmentManager, DatePickerFragment.TAG);
    }
}
```

## <a name="summary"></a>Récapitulatif

Cet exemple a expliqué comment afficher un `DatePicker` widget sous la forme d’une boîte de dialogue modale contextuelle dans le cadre d’une activité Android. Il a fourni un exemple d’implémentation de DialogFragment `IOnDateSetListener` et a abordé l’interface. Cet exemple montre également comment le DialogFragment peut interagir avec l’activité de l’hôte pour afficher la date sélectionnée.

## <a name="related-links"></a>Liens associés

- [DialogFragment](xref:Android.App.DialogFragment)
- [DatePicker](xref:Android.Widget.DatePicker)
- [DatePickerDialog](xref:Android.App.DatePickerDialog)
- [DatePickerDialog.IOnDateSetListener](xref:Android.App.DatePickerDialog.IOnDateSetListener)
- [Sélectionner une date](https://github.com/xamarin/recipes/tree/master/Recipes/android/controls/datepicker/select_a_date)
