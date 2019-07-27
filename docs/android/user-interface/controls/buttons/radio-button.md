---
title: RadioButton
ms.prod: xamarin
ms.assetid: 3C32EA3F-D917-C988-72C5-A17354DA791E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 2279282b08c9d97b239de424cf38aa6f1463dc4d
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68510356"
---
# <a name="radiobutton"></a>RadioButton

Dans cette section, vous allez créer deux cases d’option mutuellement exclusives (l’activation de l’une d’elles désactive l’autre) à l’aide de l’option[`RadioGroup`](xref:Android.Widget.RadioGroup)
les[`RadioButton`](xref:Android.Widget.RadioButton)
widgets. Quand vous appuyez sur une case d’option, un message de Toast s’affiche.


Ouvrez le fichier Resources **/Layout/main. AXML** et [`RadioButton`](xref:Android.Widget.RadioButton)ajoutez deux s, imbriqués [`RadioGroup`](xref:Android.Widget.RadioGroup) dans un ( [`LinearLayout`](xref:Android.Widget.LinearLayout)à l’intérieur du):

```xml
<RadioGroup
  android:layout_width="fill_parent"
  android:layout_height="wrap_content"
  android:orientation="vertical">
  <RadioButton android:id="@+id/radio_red"
      android:layout_width="wrap_content"
      android:layout_height="wrap_content"
      android:text="Red" />
  <RadioButton android:id="@+id/radio_blue"
      android:layout_width="wrap_content"
      android:layout_height="wrap_content"
      android:text="Blue" />
</RadioGroup>
```

Il est important que les [`RadioButton`](xref:Android.Widget.RadioButton)s soient regroupées par l' [`RadioGroup`](xref:Android.Widget.RadioGroup) élément afin qu’il ne puisse pas en sélectionner plus d’une à la fois. Cette logique est gérée automatiquement par le système Android. Lorsqu’un[`RadioButton`](xref:Android.Widget.RadioButton)
dans un groupe est sélectionné, tous les autres sont automatiquement désélectionnés.

Pour effectuer une opération lorsque [`RadioButton`](xref:Android.Widget.RadioButton) chaque est sélectionné, nous devons écrire un gestionnaire d’événements:

```csharp
private void RadioButtonClick (object sender, EventArgs e)
{
    RadioButton rb = (RadioButton)sender;
    Toast.MakeText (this, rb.Text, ToastLength.Short).Show ();
}
```

Tout d’abord, l’expéditeur passé est converti en RadioButton.
Ensuite, un[`Toast`](xref:Android.Widget.Toast)
message affiche le texte de la case d’option sélectionnée.

Maintenant, en bas de la[`OnCreate()`](xref:Android.App.Activity.OnCreate*)
, ajoutez ce qui suit:

```csharp
RadioButton radio_red = FindViewById<RadioButton>(Resource.Id.radio_red);
RadioButton radio_blue = FindViewById<RadioButton>(Resource.Id.radio_blue);

radio_red.Click += RadioButtonClick;
radio_blue.Click += RadioButtonClick;
```

Cela permet de capturer chacun des [`RadioButton`](xref:Android.Widget.RadioButton)s de la disposition et d’ajouter l’événement handlerto qui vient d’être créé.

Exécutez l'application.

> [!TIP]
> Si vous avez besoin de modifier l’état vous-même (par exemple, [`CheckBoxPreference`](xref:Android.Preferences.CheckBoxPreference)lors du chargement d’un enregistrement), utilisez l'[`Checked`](xref:Android.Widget.CompoundButton.Checked)
> méthode setter de propriété ou[`Toggle()`](xref:Android.Widget.CompoundButton.Toggle)
> .

*Certaines parties de cette page sont des modifications basées sur le travail créé et partagé par le projet open source Android et utilisées conformément aux termes décrits dans la*
[*licence d’attribution de Creative-2,5*](http://creativecommons.org/licenses/by/2.5/). 
