---
title: ToggleButton
ms.prod: xamarin
ms.assetid: 9ADA8FCF-63ED-897A-DD56-D7D86535A92C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/06/2018
ms.openlocfilehash: fe444d255beb9c08b4b5bcf5de36a8740e503b55
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029292"
---
# <a name="togglebutton"></a>ToggleButton

Dans cette section, vous allez créer un bouton utilisé spécifiquement pour basculer entre deux États, à l’aide du widget [`ToggleButton`](xref:Android.Widget.ToggleButton) . Ce widget constitue une excellente alternative aux cases d’option si vous avez deux États simples qui s’excluent mutuellement (« on » et « OFF », par exemple). Android 4,0 (API de niveau 14) a introduit une alternative au bouton bascule appelé [`Switch`](xref:Android.Widget.Switch).

Un exemple de contrôle **ToggleButton** peut être affiché dans la paire d’images de gauche, tandis que la paire d’images de droite présente un exemple de **commutateur**:

![Exemples de commutateurs et de ToggleButtons dans les États activés et désactivés](toggle-button-images/togglebutton-switch.png)  

Le contrôle utilisé par une application est une question de style. Les deux widgets sont fonctionnellement équivalents.

Ouvrez le fichier **Resources/layout/main. AXML** et ajoutez l’élément [`ToggleButton`](xref:Android.Widget.ToggleButton) (à l’intérieur du [`LinearLayout`](xref:Android.Widget.LinearLayout)) :

Pour effectuer une opération lorsque l’État est modifié, ajoutez le code suivant à la fin de l' [`OnCreate()`](xref:Android.App.Activity.OnCreate*)
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

Cette opération capture l’élément [`ToggleButton`](xref:Android.Widget.ToggleButton) à partir de la disposition et gère l’événement Click, qui définit l’action à effectuer lorsque l’utilisateur clique sur le bouton. Dans cet exemple, la méthode vérifie le nouvel État du bouton, puis affiche un message [`Toast`](xref:Android.Widget.Toast) qui indique l’état actuel.

Notez que le [`ToggleButton`](xref:Android.Widget.ToggleButton) gère son propre changement d’État entre les cases cochées et désactivées. il vous suffit de le demander.

Exécutez l'application.

> [!TIP]
> Si vous avez besoin de modifier l’état vous-même (par exemple, lors du chargement d’un [`CheckBoxPreference`](xref:Android.Preferences.CheckBoxPreference)enregistré), utilisez la [`Checked`](xref:Android.Widget.CompoundButton.Checked)
> accesseur set ou [`Toggle()`](xref:Android.Widget.CompoundButton.Toggle) de propriété
> .

## <a name="related-links"></a>Liens associés

- [Contrôle](https://developer.android.com/reference/android/widget/ToggleButton.html)
- [Commutateur](https://developer.android.com/reference/android/widget/Switch.html)
