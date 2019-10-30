---
title: Xamarin. Android (commutateur)
description: Comment utiliser le widget commutateur dans une application Xamarin. Android
ms.prod: xamarin
ms.assetid: 6E1F3324-EC41-454A-AEC0-0208813C7E50
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/29/2018
ms.openlocfilehash: 73becb5e4424854c9be6cdc3554f6cf93507b9a9
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029137"
---
# <a name="xamarinandroid-switch"></a>Xamarin. Android (commutateur)

Le widget `Switch` (indiqué ci-dessous) permet à un utilisateur de basculer entre deux États, comme ON ou OFF. La valeur par défaut de `Switch` est OFF. Le widget est affiché ci-dessous dans l’état activé/désactivé :

[![des captures d’écran d’un widget de commutateur dans les États OFF et ON](switch-images/16-switch-onoff.png)](switch-images/16-switch-onoff.png#lightbox)

## <a name="creating-a-switch"></a>Création d’un commutateur

Pour créer un commutateur, il vous suffit de déclarer un élément `Switch` dans XML comme suit :

```xml
<Switch android:layout_width="wrap_content"
        android:layout_height="wrap_content" />
```

Cela crée un commutateur de base comme indiqué ci-dessous :

[![capture d’écran de l’application de démonstration affichant un commutateur en état désactivé](switch-images/07-switch.png)](switch-images/07-switch.png#lightbox)

## <a name="changing-default-values"></a>Modification des valeurs par défaut

Le texte affiché par le contrôle pour les États ACTIVÉs et désactivés et la valeur par défaut est configurable. Par exemple, pour activer le commutateur par défaut et lire non/oui au lieu de OFF/ON, nous pouvons définir les attributs `checked`, `textOn`et `textOff` dans le code XML suivant.

```xml
<Switch android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:checked="true"
        android:textOn="YES"
        android:textOff="NO" />
```

## <a name="providing-a-title"></a>Fournir un titre

Le widget `Switch` prend également en charge l’ajout d’une étiquette de texte en définissant l’attribut `text` comme suit :

```xml
<Switch android:text="Is Xamarin.Android great?"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:checked="true"
        android:textOn="YES"
        android:textOff="NO" />
```

Ce balisage produit la capture d’écran suivante au moment de l’exécution :

[Capture d’écran ![de l’application de démonstration avec texte horizontalement précédant le widget de commutateur](switch-images/08-switch.png)](switch-images/08-switch.png#lightbox)

Quand la valeur d’un `Switch`change, elle déclenche un événement `CheckedChange`.
Par exemple, dans le code suivant, nous capturons cet événement et présentons un widget `Toast` avec un message basé sur la valeur `isChecked` de `Switch`, qui est passée au gestionnaire d’événements dans le cadre de l’argument `CompoundButton.CheckedChangeEventArg`.

```csharp
Switch s = FindViewById<Switch> (Resource.Id.monitored_switch);
           
s.CheckedChange += delegate(object sender, CompoundButton.CheckedChangeEventArgs e) {
    var toast = Toast.MakeText (this, "Your answer is " +
        (e.IsChecked ?  "correct" : "incorrect"), ToastLength.Short);
    toast.Show ();
};
```

## <a name="related-links"></a>Liens associés

- [SwitchDemo (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/switchdemo)
- [Didacticiel sur la disposition des onglets](~/android/user-interface/layouts/tab-layout/index.md)
