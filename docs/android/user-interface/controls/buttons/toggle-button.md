---
title: ToggleButton
ms.prod: xamarin
ms.assetid: 9ADA8FCF-63ED-897A-DD56-D7D86535A92C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 56372bb643cab545529d6a4a89c804471f3344bc
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70762447"
---
# <a name="togglebutton"></a>ToggleButton

Dans cette section, vous allez créer un bouton utilisé spécifiquement pour basculer entre deux États, à l' [`ToggleButton`](xref:Android.Widget.ToggleButton) aide du widget. Ce widget constitue une excellente alternative aux cases d’option si vous avez deux États simples qui s’excluent mutuellement (« on » et « OFF », par exemple). Android 4,0 (API de niveau 14) a introduit une alternative au bouton bascule appelé a [`Switch`](xref:Android.Widget.Switch).

Un exemple de contrôle **ToggleButton** peut être affiché dans la paire d’images de gauche, tandis que la paire d’images de droite présente un exemple de **commutateur**:

![Exemples de commutateurs et de ToggleButtons dans les États activés et désactivés](toggle-button-images/togglebutton-switch.png)  

Le contrôle utilisé par une application est une question de style. Les deux widgets sont fonctionnellement équivalents.

Ouvrez le fichier **Resources/layout/main. AXML** et [`ToggleButton`](xref:Android.Widget.ToggleButton) ajoutez l’élément ( [`LinearLayout`](xref:Android.Widget.LinearLayout)à l’intérieur du) :

Pour effectuer une opération lorsque l’État est modifié, ajoutez le code suivant à la fin de la[`OnCreate()`](xref:Android.App.Activity.OnCreate*)
méthode

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

Cette opération capture l' [`ToggleButton`](xref:Android.Widget.ToggleButton) élément à partir de la disposition et gère l’événement Click, qui définit l’action à effectuer lorsque l’utilisateur clique sur le bouton. Dans cet exemple, la méthode vérifie le nouvel État du bouton, puis affiche un [`Toast`](xref:Android.Widget.Toast) message qui indique l’état actuel.

Notez que le [`ToggleButton`](xref:Android.Widget.ToggleButton) gère sa propre modification d’État entre les cases cochées et désactivées. il vous suffit de le demander.

Exécutez l'application.

> [!TIP]
> Si vous avez besoin de modifier l’état vous-même (par exemple, [`CheckBoxPreference`](xref:Android.Preferences.CheckBoxPreference)lors du chargement d’un enregistrement), utilisez l'[`Checked`](xref:Android.Widget.CompoundButton.Checked)
> méthode setter de propriété ou[`Toggle()`](xref:Android.Widget.CompoundButton.Toggle)
> .

## <a name="related-links"></a>Liens associés

- [Contrôle](https://developer.android.com/reference/android/widget/ToggleButton.html)
- [Commutateur](https://developer.android.com/reference/android/widget/Switch.html)
