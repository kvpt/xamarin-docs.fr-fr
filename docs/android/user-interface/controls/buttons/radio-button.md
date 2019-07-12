---
title: RadioButton
ms.prod: xamarin
ms.assetid: 3C32EA3F-D917-C988-72C5-A17354DA791E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: ea8af3c84bb53434489d230d159a27b6b9d500d8
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67830258"
---
# <a name="radiobutton"></a>RadioButton

Dans cette section, vous allez créer deux boutons de case d’option mutuellement exclusives (désactive l’activation de l’un l’autre), en utilisant le [`RadioGroup`](https://developer.xamarin.com/api/type/Android.Widget.RadioGroup/)
Et [`RadioButton`](https://developer.xamarin.com/api/type/Android.Widget.RadioButton/)
widgets. Lorsque l’un des boutons radio sont enfoncée, un message toast s’affichera.


Ouvrir le **Resources/layout/Main.axml** fichier, puis ajoutez deux [ `RadioButton` ](https://developer.xamarin.com/api/type/Android.Widget.RadioButton/)s, imbriqué dans un [ `RadioGroup` ](https://developer.xamarin.com/api/type/Android.Widget.RadioGroup/) (à l’intérieur de la [ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)):

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

Il est important que le [ `RadioButton` ](https://developer.xamarin.com/api/type/Android.Widget.RadioButton/)s sont regroupés par le [ `RadioGroup` ](https://developer.xamarin.com/api/type/Android.Widget.RadioGroup/) élément afin que ne plusieurs peuvent être sélectionné simultanément. Cette logique est gérée automatiquement par le système Android. Lorsqu’un [`RadioButton`](https://developer.xamarin.com/api/type/Android.Widget.RadioButton/)
au sein d’un groupe est sélectionné, toutes les autres sont automatiquement désélectionnées.

Effectuer une action lorsque chaque [ `RadioButton` ](https://developer.xamarin.com/api/type/Android.Widget.RadioButton/) est sélectionné, nous devons écrire un gestionnaire d’événements :

```csharp
private void RadioButtonClick (object sender, EventArgs e)
{
    RadioButton rb = (RadioButton)sender;
    Toast.MakeText (this, rb.Text, ToastLength.Short).Show ();
}
```

Tout d’abord, l’expéditeur qui est passé est converti dans un contrôle RadioButton.
Un [`Toast`](https://developer.xamarin.com/api/type/Android.Widget.Toast/)
message affiche le texte du bouton de case d’option sélectionnée.

Maintenant, au bas de la [`OnCreate()`](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/Android.OS.PersistableBundle)
méthode, ajoutez le code suivant :

```csharp
RadioButton radio_red = FindViewById<RadioButton>(Resource.Id.radio_red);
RadioButton radio_blue = FindViewById<RadioButton>(Resource.Id.radio_blue);

radio_red.Click += RadioButtonClick;
radio_blue.Click += RadioButtonClick;
```

Chaque capture le [ `RadioButton` ](https://developer.xamarin.com/api/type/Android.Widget.RadioButton/)s à partir de la mise en page et ajoute chaque handlerto événement nouvellement créé.

Exécutez l'application.

> [!TIP]
> Si vous avez besoin modifier l’état vous-même (par exemple lors du chargement une enregistré [ `CheckBoxPreference` ](https://developer.xamarin.com/api/type/Android.Preferences.CheckBoxPreference/)), utiliser la [`Checked`](https://developer.xamarin.com/api/property/Android.Widget.CompoundButton.Checked/)
> accesseur Set de propriété ou [`Toggle()`](https://developer.xamarin.com/api/member/Android.Widget.CompoundButton.Toggle/)
> .

*Certaines parties de cette page sont des modifications basées sur le travail créé et partagé par Android Open Source Project et utilisé conformément aux conditions décrites dans le*
[*licence Creative Commons 2.5 Attribution* ](http://creativecommons.org/licenses/by/2.5/). 
