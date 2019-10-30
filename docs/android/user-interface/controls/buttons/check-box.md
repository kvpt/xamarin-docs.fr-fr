---
title: Case à cocher
ms.prod: xamarin
ms.assetid: A884AF10-D5EA-72CA-2301-B80CEC7FFBE7
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/06/2018
ms.openlocfilehash: 06908ad8993eb6d47476006b23865fa1c7fe694f
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029345"
---
# <a name="checkbox"></a>Case à cocher

Dans cette section, vous allez créer une case à cocher pour sélectionner des éléments, à l’aide du widget [`CheckBox`](xref:Android.Widget.CheckBox) . Lorsque vous appuyez sur la case à cocher, un message de Toast indique l’état actuel de la case à cocher.

Ouvrez le fichier **Resources/layout/main. AXML** et ajoutez l’élément [`CheckBox`](xref:Android.Widget.CheckBox) (à l’intérieur du [`LinearLayout`](xref:Android.Widget.LinearLayout)) :

```xml
<CheckBox android:id="@+id/checkbox"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="check it out" />
```

Pour effectuer une opération lorsque l’État est modifié, ajoutez le code suivant à la fin de la méthode [`OnCreate()`](xref:Android.App.Activity.OnCreate*) :

```csharp
CheckBox checkbox = FindViewById<CheckBox>(Resource.Id.checkbox);

checkbox.Click += (o, e) => {
    if (checkbox.Checked)
        Toast.MakeText (this, "Selected", ToastLength.Short).Show ();
    else
        Toast.MakeText (this, "Not selected", ToastLength.Short).Show ();
};
```

Cette opération capture l’élément [`CheckBox`](xref:Android.Widget.CheckBox) à partir de la disposition, puis gère l’événement Click, qui définit l’action à effectuer lorsque l’utilisateur clique sur la case à cocher. Lorsque vous cliquez dessus, la propriété [`Checked`](xref:Android.Widget.CompoundButton.Checked) est appelée pour vérifier le nouvel état de la case à cocher. S’il a été activé, un [`Toast`](xref:Android.Widget.Toast) affiche le message « sélectionné », sinon il affiche « non sélectionné ». Le [`CheckBox`](xref:Android.Widget.CheckBox) gère ses propres modifications d’État, vous n’avez donc qu’à interroger l’état actuel.

Exécutez-le.

> [!TIP]
> Si vous avez besoin de modifier l’état vous-même (par exemple, lors du chargement d’un [`CheckBoxPreference`](xref:Android.Preferences.CheckBoxPreference)enregistré, utilisez la méthode setter de la propriété [`Checked`](xref:Android.Widget.CompoundButton.Checked) ou [`Toggle()`](xref:Android.Widget.CompoundButton.Toggle) méthode.

*Certaines parties de cette page sont des modifications basées sur le travail créé et partagé par le projet open source Android et utilisées conformément aux termes décrits dans la licence d'* [*attribution de Creative-2,5*](https://creativecommons.org/licenses/by/2.5/).
