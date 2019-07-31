---
title: Xamarin. Android (commutateur)
description: Comment utiliser le widget commutateur dans une application Xamarin. Android
ms.prod: xamarin
ms.assetid: 6E1F3324-EC41-454A-AEC0-0208813C7E50
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/29/2018
ms.openlocfilehash: 7fe4ffc733a71ed0f372da8288c7f1b63a961442
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68642571"
---
# <a name="xamarinandroid-switch"></a>Xamarin. Android (commutateur)

Le `Switch` widget (illustré ci-dessous) permet à un utilisateur de basculer entre deux États, comme on ou OFF. La `Switch` valeur par défaut est OFF. Le widget est affiché ci-dessous dans l’état activé/désactivé:

[![Captures d’écran d’un widget de commutateur en désactivant et sur les États](switch-images/16-switch-onoff.png)](switch-images/16-switch-onoff.png#lightbox)

## <a name="creating-a-switch"></a>Création d’un commutateur

Pour créer un commutateur, il vous suffit `Switch` de déclarer un élément dans XML comme suit:

```xml
<Switch android:layout_width="wrap_content"
        android:layout_height="wrap_content" />
```

Cela crée un commutateur de base comme indiqué ci-dessous:

[![Capture d’écran de l’application de démonstration affichant un commutateur en état désactivé](switch-images/07-switch.png)](switch-images/07-switch.png#lightbox)

## <a name="changing-default-values"></a>Modification des valeurs par défaut

Le texte affiché par le contrôle pour les États ACTIVÉs et désactivés et la valeur par défaut est configurable. Par exemple, pour activer le commutateur par défaut et en lecture non/oui au lieu de off/on, nous pouvons définir `checked`les `textOn`attributs, `textOff` et dans le code XML suivant.

```xml
<Switch android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:checked="true"
        android:textOn="YES"
        android:textOff="NO" />
```



## <a name="providing-a-title"></a>Fournir un titre

Le `Switch` widget prend également en charge l’ajout d’une étiquette `text` de texte en définissant l’attribut comme suit:

```xml
<Switch android:text="Is Xamarin.Android great?"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:checked="true"
        android:textOn="YES"
        android:textOff="NO" />
```

Ce balisage produit la capture d’écran suivante au moment de l’exécution:

[![Capture d’écran de l’application de démonstration avec texte horizontalement précédant le widget de commutateur](switch-images/08-switch.png)](switch-images/08-switch.png#lightbox)

Lorsque la valeur de est modifiée, elle déclenche `CheckedChange` un événement. `Switch`
Par exemple, dans le code suivant, nous capturons cet événement et `Toast` présentons un widget avec un message `isChecked` basé sur `Switch`la valeur de, qui est transmise au gestionnaire d’événements `CompoundButton.CheckedChangeEventArg` dans le cadre de l’argument.

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
