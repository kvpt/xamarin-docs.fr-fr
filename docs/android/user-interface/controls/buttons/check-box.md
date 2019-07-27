---
title: Case à cocher
ms.prod: xamarin
ms.assetid: A884AF10-D5EA-72CA-2301-B80CEC7FFBE7
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: f6f594d86cab8b1173ee9f67402862e1ec2890b2
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68510374"
---
# <a name="checkbox"></a>Case à cocher

Dans cette section, vous allez créer une case à cocher pour sélectionner des éléments, à l’aide de l’option[`CheckBox`](xref:Android.Widget.CheckBox)
sélection. Lorsque vous appuyez sur la case à cocher, un message de Toast indique l’état actuel de la case à cocher.

Ouvrez le fichier Resources **/Layout/main. AXML** et [`CheckBox`](xref:Android.Widget.CheckBox) ajoutez l’élément ( [`LinearLayout`](xref:Android.Widget.LinearLayout)à l’intérieur du):

```xml
<CheckBox android:id="@+id/checkbox"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="check it out" />
```

Pour effectuer une opération lorsque l’État est modifié, ajoutez le code suivant à la fin de la[`OnCreate()`](xref:Android.App.Activity.OnCreate*)
méthode

```csharp
CheckBox checkbox = FindViewById<CheckBox>(Resource.Id.checkbox);

checkbox.Click += (o, e) => {
    if (checkbox.Checked)
        Toast.MakeText (this, "Selected", ToastLength.Short).Show ();
    else
        Toast.MakeText (this, "Not selected", ToastLength.Short).Show ();
};
```

Cela capture le[`CheckBox`](xref:Android.Widget.CheckBox)
élément de la disposition, puis gère l’événement Click, qui définit l’action à effectuer lorsque l’utilisateur clique sur la case à cocher. Lorsque vous cliquez dessus, le[`Checked`](xref:Android.Widget.CompoundButton.Checked)
la propriété est appelée pour vérifier le nouvel état de la case à cocher. Si elle a été activée, une[`Toast`](xref:Android.Widget.Toast)
affiche le message «sélectionné», sinon il affiche «non sélectionné». La[`CheckBox`](xref:Android.Widget.CheckBox)
gère ses propres modifications d’État, de sorte que vous devez uniquement interroger l’état actuel.

Exécutez-le.

> [!TIP]
> Si vous devez modifier l’état vous-même (par exemple, lors du [`CheckBoxPreference`](xref:Android.Preferences.CheckBoxPreference)chargement d’un enregistré), utilisez l'[`Checked`](xref:Android.Widget.CompoundButton.Checked)
> méthode setter de propriété ou[`Toggle()`](xref:Android.Widget.CompoundButton.Toggle)
> .

*Certaines parties de cette page sont des modifications basées sur le travail créé et partagé par le projet open source Android et utilisées conformément aux termes décrits dans la*
[*licence d’attribution de Creative-2,5*](http://creativecommons.org/licenses/by/2.5/).
