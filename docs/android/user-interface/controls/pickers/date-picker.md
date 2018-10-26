---
title: Sélecteur de dates
description: Sélection des Dates de calendrier à l’aide de la DatePickerDialog et DialogFragment
ms.prod: xamarin
ms.assetid: F2BCD8D4-8957-EA53-C5A8-6BB603ADB47B
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 01/22/2018
ms.openlocfilehash: 9f82317f6041de3952d11b391afffafe6fbd8761
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50122970"
---
# <a name="date-picker"></a>Sélecteur de dates

## <a name="overview"></a>Vue d'ensemble

Il existe des occasions quand un utilisateur doit entrer des données dans une application Android. Pour faciliter cela, le framework Android fournit le [ `DatePicker` ](https://developer.xamarin.com/api/type/Android.Widget.DatePicker/) widget et le [ `DatePickerDialog` ](https://developer.xamarin.com/api/type/Android.App.DatePickerDialog/) . Le `DatePicker` permet aux utilisateurs de sélectionner l’année, le mois et le jour dans une interface cohérente entre appareils et applications. Le `DatePickerDialog` est une classe d’assistance qui encapsule le `DatePicker` dans une boîte de dialogue.

Les applications Android modernes doivent afficher le `DatePickerDialog` dans un [ `DialogFragment` ](https://developer.xamarin.com/api/type/Android.App.DialogFragment/). Cela permettra à une application afficher le sélecteur de dates comme une boîte de dialogue contextuelle ou incorporé dans une activité. En outre, le `DialogFragment` gère le cycle de vie et l’affichage de la boîte de dialogue, ce qui réduit la quantité de code qui doit être implémentée.

Ce guide va vous montrer comment utiliser le `DatePickerDialog`, encapsulé dans un `DialogFragment`. L’exemple d’application affichera la `DatePickerDialog` en tant que boîte de dialogue modale lorsque l’utilisateur clique sur un bouton sur une activité. Lorsque la date est définie par l’utilisateur, un `TextView` met à jour avec la date à laquelle a été sélectionnée.

[![Bouton de capture d’écran de prélèvement Date puis par boîte de dialogue de sélecteur de dates](date-picker-images/image-01-sml.png)](date-picker-images/image-01.png#lightbox)

## <a name="requirements"></a>Configuration requise

L’exemple d’application pour ce guide cible Android 4.1 (niveau d’API
16) ou version ultérieure, mais s’applique à Android 3.0 (niveau d’API 11 ou version ultérieure). Il est possible de prendre en charge les versions antérieures d’Android avec l’ajout de la bibliothèque de prise en charge Android v4 au projet et certaines modifications de code.

## <a name="using-the-datepicker"></a>À l’aide de DatePicker

Cet exemple étend `DialogFragment`. La sous-classe hébergera et afficher un `DatePickerDialog`:

![Boîte de dialogue de gros plan de sélecteur de dates](date-picker-images/image-02.png)

Lorsque l’utilisateur sélectionne une date et clique sur le **OK** bouton, le `DatePickerDialog` appellera la méthode [ `IOnDateSetListener.OnDateSet` ](https://developer.xamarin.com/api/member/Android.App.DatePickerDialog+IOnDateSetListener.OnDateSet/p/Android.Widget.DatePicker/System.Int32/System.Int32/System.Int32/).
Cette interface est implémentée par l’hébergement `DialogFragment`. Si l’utilisateur clique sur le **Annuler** bouton, puis de la boîte de dialogue et le fragment fait disparaître eux-mêmes.

Il existe plusieurs façons le `DialogFragment` peut retourner la date sélectionnée à l’activité d’hébergement :

1. **Appeler une méthode ou de définir une propriété** &ndash; l’activité peut fournir une propriété ou méthode spécifiquement pour la définition de cette valeur.

2. **Déclencher un événement** &ndash; le `DialogFragment` peut définir un événement qui sera déclenché lorsque `OnDateSet` est appelé.

3. **Utilisez un `Action`**  &ndash; le `DialogFragment` peut appeler un `Action<DateTime>` pour afficher la date dans l’activité. L’activité fournira la `Action<DateTime` lors de l’instanciation du `DialogFragment`. Cet exemple utilise la troisième technique et demander à ce que l’activité fournissent un `Action<DateTime>` à la `DialogFragment`.



### <a name="extending-dialogfragment"></a>Extension DialogFragment

La première étape dans l’affichage un `DatePickerDialog` consiste à la sous-classe `DialogFragment` et qu’elle implémente le `IOnDateSetListener` interface :

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

Le `NewInstance` méthode est appelée pour instancier un nouveau `DatePickerFragment`. Cette méthode prend un `Action<DateTime>` qui sera appelé lorsque l’utilisateur clique sur le **OK** situé dans le `DatePickerDialog`.

Quand le fragment doit être affichée, Android appelle la méthode `OnCreateDialog`. Cette méthode crée un nouveau `DatePickerDialog` de l’objet et l’initialiser avec la date actuelle et l’objet de rappel (qui est l’instance actuelle de la `DatePickerFragment`).


> [!NOTE]
> N’oubliez pas que la valeur du mois lorsque `IOnDateSetListener.OnDateSet` est appelé est dans la plage de 0 à 11 et pas de 1 à 12. Le jour du mois sera dans la plage de 1 à 31 (selon le mois a été sélectionné).



### <a name="showing-the-datepickerfragment"></a>Affichant le DatePickerFragment

Maintenant que le `DialogFragment` a été implémenté, cette section examine comment utiliser le fragment dans une activité. Dans l’exemple d’application qui accompagne ce guide, l’activité instanciera le `DialogFragment` à l’aide de la `NewInstance` méthode de fabrique et l’affichage puis il appelle `DialogFragment.Show`. Dans le cadre de l’instanciation le `DialogFragment`, l’activité transmet un `Action<DateTime>`, qui affiche la date dans un `TextView` qui est hébergé par l’activité :

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

Cet exemple décrit comment afficher un `DatePicker` widget comme une boîte de dialogue modale de menu contextuel dans le cadre d’une activité Android. Il fourni un exemple d’implémentation DialogFragment et abordé le `IOnDateSetListener` interface. Cet exemple a également démontré comment le DialogFragment peut interagir avec l’hôte de l’activité pour afficher la date sélectionnée.


## <a name="related-links"></a>Liens associés

- [DialogFragment](https://developer.xamarin.com/api/type/Android.App.DialogFragment/)
- [DatePicker](https://developer.xamarin.com/api/type/Android.Widget.DatePicker/)
- [DatePickerDialog](https://developer.xamarin.com/api/type/Android.App.DatePickerDialog/)
- [DatePickerDialog.IOnDateSetListener](https://developer.xamarin.com/api/type/Android.App.DatePickerDialog+IOnDateSetListener/)
- [Sélectionnez une Date](https://github.com/xamarin/recipes/tree/master/Recipes/android/controls/datepicker/select_a_date)
