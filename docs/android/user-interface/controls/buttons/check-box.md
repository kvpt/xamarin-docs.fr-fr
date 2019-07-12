---
title: Case à cocher
ms.prod: xamarin
ms.assetid: A884AF10-D5EA-72CA-2301-B80CEC7FFBE7
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 9f2fd10e5cfe28206d323b2769517c2584919232
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67829725"
---
# <a name="checkbox"></a>Case à cocher

Dans cette section, vous allez créer une case à cocher pour sélectionner des éléments, en utilisant le [`CheckBox`](https://developer.xamarin.com/api/type/Android.Widget.CheckBox)
widget. Lorsque vous appuyez sur la case à cocher, un message toast indiquera l’état actuel de la case à cocher.

Ouvrir le **Resources/layout/Main.axml** fichier, puis ajoutez le [ `CheckBox` ](https://developer.xamarin.com/api/type/Android.Widget.CheckBox/) élément (à l’intérieur de la [ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout)) :

```xml
<CheckBox android:id="@+id/checkbox"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="check it out" />
```

Pour faire quelque chose lorsque l’état est modifié, ajoutez le code suivant à la fin de la [`OnCreate()`](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/Android.OS.PersistableBundle)
méthode :

```csharp
CheckBox checkbox = FindViewById<CheckBox>(Resource.Id.checkbox);

checkbox.Click += (o, e) => {
    if (checkbox.Checked)
        Toast.MakeText (this, "Selected", ToastLength.Short).Show ();
    else
        Toast.MakeText (this, "Not selected", ToastLength.Short).Show ();
};
```

Il capture le [`CheckBox`](https://developer.xamarin.com/api/type/Android.Widget.CheckBox/)
élément à partir de la disposition, gère ensuite l’événement Click, qui définit l’action à effectuer lorsque l’utilisateur clique sur la case à cocher. Lorsque vous cliquez dessus, le [`Checked`](https://developer.xamarin.com/api/property/Android.Widget.CompoundButton.Checked/)
propriété est appelée pour vérifier le nouvel état de la case à cocher. Si elle a été activée, puis un [`Toast`](https://developer.xamarin.com/api/type/Android.Widget.Toast/)
Affiche le message « Selected », sinon qu'il affiche « Désactivée ». Le [`CheckBox`](https://developer.xamarin.com/api/type/Android.Widget.CheckBox/)
gère les modifications de son propre état, vous devez uniquement interroger l’état actuel.

Exécutez-le.

> [!TIP]
> Si vous avez besoin modifier l’état vous-même (par exemple lors du chargement une enregistré [ `CheckBoxPreference` ](https://developer.xamarin.com/api/type/Android.Preferences.CheckBoxPreference), utiliser la [`Checked`](https://developer.xamarin.com/api/property/Android.Widget.CompoundButton.Checked)
> accesseur Set de propriété ou [`Toggle()`](https://developer.xamarin.com/api/member/Android.Widget.CompoundButton.Toggle)
> .

*Certaines parties de cette page sont des modifications basées sur le travail créé et partagé par Android Open Source Project et utilisé conformément aux conditions décrites dans le*
[*licence Creative Commons 2.5 Attribution* ](http://creativecommons.org/licenses/by/2.5/).
