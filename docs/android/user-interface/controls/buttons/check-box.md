---
title: Case à cocher
ms.prod: xamarin
ms.assetid: A884AF10-D5EA-72CA-2301-B80CEC7FFBE7
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: b947217706fc8ef7ce7945bf4c88349f4367ffcd
ms.sourcegitcommit: cf56d2bae34dc0f8e94c2d3d28d5f460d59807bf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2019
ms.locfileid: "70985675"
---
# <a name="checkbox"></a>Case à cocher

Dans cette section, vous allez créer une case à cocher pour sélectionner des [`CheckBox`](xref:Android.Widget.CheckBox) éléments, à l’aide du widget. Lorsque vous appuyez sur la case à cocher, un message de Toast indique l’état actuel de la case à cocher.

Ouvrez le fichier **Resources/layout/main. AXML** et [`CheckBox`](xref:Android.Widget.CheckBox) ajoutez l’élément ( [`LinearLayout`](xref:Android.Widget.LinearLayout)à l’intérieur du) :

```xml
<CheckBox android:id="@+id/checkbox"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="check it out" />
```

Pour effectuer une opération lorsque l’État est modifié, ajoutez le code suivant à la fin de [`OnCreate()`](xref:Android.App.Activity.OnCreate*) la méthode :

```csharp
CheckBox checkbox = FindViewById<CheckBox>(Resource.Id.checkbox);

checkbox.Click += (o, e) => {
    if (checkbox.Checked)
        Toast.MakeText (this, "Selected", ToastLength.Short).Show ();
    else
        Toast.MakeText (this, "Not selected", ToastLength.Short).Show ();
};
```

Cette opération capture l' [`CheckBox`](xref:Android.Widget.CheckBox) élément à partir de la disposition, puis gère l’événement Click, qui définit l’action à effectuer lorsque l’utilisateur clique sur la case à cocher. Lorsque vous cliquez dessus, [`Checked`](xref:Android.Widget.CompoundButton.Checked) la propriété est appelée pour vérifier le nouvel état de la case à cocher. S’il a été activé, un [`Toast`](xref:Android.Widget.Toast) affiche le message « sélectionné », sinon il affiche « non sélectionné ». Le [`CheckBox`](xref:Android.Widget.CheckBox) gère ses propres modifications d’État. vous n’avez donc qu’à interroger l’état actuel.

Exécutez-le.

> [!TIP]
> Si vous devez modifier l’état vous-même (par exemple, lors du [`CheckBoxPreference`](xref:Android.Preferences.CheckBoxPreference)chargement d’un [`Checked`](xref:Android.Widget.CompoundButton.Checked) enregistré), [`Toggle()`](xref:Android.Widget.CompoundButton.Toggle) utilisez la méthode ou l’accesseur Set de propriété.

*Certaines parties de cette page sont des modifications basées sur le travail créé et partagé par le projet open source Android et utilisées conformément aux termes décrits dans la* [*Licence d’attribution Creative-2,5*](http://creativecommons.org/licenses/by/2.5/).
