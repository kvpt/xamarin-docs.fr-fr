---
title: RadioButton
ms.prod: xamarin
ms.assetid: 3C32EA3F-D917-C988-72C5-A17354DA791E
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/06/2018
ms.openlocfilehash: 9f51adcbd1accb4f780318cc0853e612ed8e5bed
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029302"
---
# <a name="radiobutton"></a>RadioButton

Dans cette section, vous allez créer deux cases d’option mutuellement exclusives (l’activation de l’une d’elles désactive l’autre) à l’aide de l' [`RadioGroup`](xref:Android.Widget.RadioGroup)
et [`RadioButton`](xref:Android.Widget.RadioButton)
widgets. Quand vous appuyez sur une case d’option, un message de Toast s’affiche.

Ouvrez le fichier **Resources/layout/main. AXML** et ajoutez deux [`RadioButton`](xref:Android.Widget.RadioButton)s, imbriqués dans un [`RadioGroup`](xref:Android.Widget.RadioGroup) (à l’intérieur du [`LinearLayout`](xref:Android.Widget.LinearLayout)) :

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

Il est important que les [`RadioButton`](xref:Android.Widget.RadioButton)s soient regroupées par l’élément [`RadioGroup`](xref:Android.Widget.RadioGroup) afin qu’il ne puisse pas en sélectionner plus d’une à la fois. Cette logique est gérée automatiquement par le système Android. Lorsqu’un [`RadioButton`](xref:Android.Widget.RadioButton)
dans un groupe est sélectionné, tous les autres sont automatiquement désélectionnés.

Pour effectuer une opération lorsque chaque [`RadioButton`](xref:Android.Widget.RadioButton) est sélectionné, nous devons écrire un gestionnaire d’événements :

```csharp
private void RadioButtonClick (object sender, EventArgs e)
{
    RadioButton rb = (RadioButton)sender;
    Toast.MakeText (this, rb.Text, ToastLength.Short).Show ();
}
```

Tout d’abord, l’expéditeur passé est converti en RadioButton.
Une [`Toast`](xref:Android.Widget.Toast)
message affiche le texte de la case d’option sélectionnée.

À présent, en bas de la [`OnCreate()`](xref:Android.App.Activity.OnCreate*)
, ajoutez ce qui suit :

```csharp
RadioButton radio_red = FindViewById<RadioButton>(Resource.Id.radio_red);
RadioButton radio_blue = FindViewById<RadioButton>(Resource.Id.radio_blue);

radio_red.Click += RadioButtonClick;
radio_blue.Click += RadioButtonClick;
```

Cela permet de capturer chaque [`RadioButton`](xref:Android.Widget.RadioButton)s à partir de la disposition et d’ajouter l’événement handlerto qui vient d’être créé.

Exécutez l'application.

> [!TIP]
> Si vous avez besoin de modifier l’état vous-même (par exemple, lors du chargement d’un [`CheckBoxPreference`](xref:Android.Preferences.CheckBoxPreference)enregistré), utilisez la [`Checked`](xref:Android.Widget.CompoundButton.Checked)
> accesseur set ou [`Toggle()`](xref:Android.Widget.CompoundButton.Toggle) de propriété
> .

*Certaines parties de cette page sont des modifications basées sur le travail créé et partagé par le projet open source Android et utilisées conformément aux termes décrits dans la*
[*licence d’attribution de Creative-2,5*](https://creativecommons.org/licenses/by/2.5/). 
