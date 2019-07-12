---
title: ToggleButton
ms.prod: xamarin
ms.assetid: 9ADA8FCF-63ED-897A-DD56-D7D86535A92C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: a22d274feb5539164663ac0c48e5a84bdf5d2c66
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67830225"
---
# <a name="togglebutton"></a>ToggleButton

Dans cette section, vous allez créer un bouton utilisé spécifiquement pour le basculement entre deux États, à l’aide de la [ `ToggleButton` ](https://developer.xamarin.com/api/type/Android.Widget.ToggleButton/) widget. Ce widget est une excellente alternative pour les cases d’option si vous avez deux États simples qui s’excluent mutuellement (« on » et « off », par exemple). Android 4.0 (API niveau 14) a introduit une alternative au bouton bascule appelé un [ `Switch` ](https://developer.xamarin.com/api/type/Android.Widget.Switch/).

Un exemple d’un **ToggleButton** sont consultables dans la paire de gauche des images, tandis que la paire de droite d’images présente un exemple d’un **commutateur**:

![Exemples de commutateurs et contrôles ToggleButton dans sur site et hors des États](toggle-button-images/togglebutton-switch.png)  

Contrôle qui utilise une application est une question de style. Les deux widgets sont fonctionnellement équivalents.

Ouvrir le **Resources/layout/Main.axml** fichier, puis ajoutez le [ `ToggleButton` ](https://developer.xamarin.com/api/type/Android.Widget.ToggleButton/) élément (à l’intérieur de la [ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)) :

Pour faire quelque chose lorsque l’état est modifié, ajoutez le code suivant à la fin de la [`OnCreate()`](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/Android.OS.PersistableBundle)
méthode :

```csharp
ToggleButton togglebutton = FindViewById<ToggleButton>(Resource.Id.togglebutton);

togglebutton.Click += (o, e) => {
    // Perform action on clicks
    if (togglebutton.Checked)
        Toast.MakeText(this, "Checked", ToastLength.Short).Show ();
    else
        Toast.MakeText(this, "Not checked", ToastLength.Short).Show ();
};
```

Il capture le [ `ToggleButton` ](https://developer.xamarin.com/api/type/Android.Widget.ToggleButton/) élément à partir de la mise en page et gère l’événement Click, qui définit l’action à effectuer lorsque le bouton est activé. Dans cet exemple, la méthode vérifie le nouvel état du bouton, puis montre un [ `Toast` ](https://developer.xamarin.com/api/type/Android.Widget.Toast/) message qui indique l’état actuel.

Notez que le [ `ToggleButton` ](https://developer.xamarin.com/api/type/Android.Widget.ToggleButton/) gère son propre état changer entre checked et unchecked, donc vous demandez ce qui est le.

Exécutez l'application.


> [!TIP]
> Si vous avez besoin modifier l’état vous-même (par exemple lors du chargement une enregistré [ `CheckBoxPreference` ](https://developer.xamarin.com/api/type/Android.Preferences.CheckBoxPreference/)), utiliser la [`Checked`](https://developer.xamarin.com/api/property/Android.Widget.CompoundButton.Checked/)
> accesseur Set de propriété ou [`Toggle()`](https://developer.xamarin.com/api/member/Android.Widget.CompoundButton.Toggle/)
> .


## <a name="related-links"></a>Liens associés

- [ToggleButton](https://developer.android.com/reference/android/widget/ToggleButton.html)
- [Commutateur](https://developer.android.com/reference/android/widget/Switch.html)
