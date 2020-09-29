---
title: Xamarin. Android (commutateur)
description: Comment utiliser le widget commutateur dans une application Xamarin. Android
ms.prod: xamarin
ms.assetid: 6E1F3324-EC41-454A-AEC0-0208813C7E50
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/29/2018
ms.openlocfilehash: dabd4b5bd7d6dbd118314e94fe04bc4623cf11e1
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457716"
---
# <a name="xamarinandroid-switch"></a>Xamarin. Android (commutateur)

Le `Switch` widget (illustré ci-dessous) permet à un utilisateur de basculer entre deux États, comme on ou OFF. La `Switch` valeur par défaut est OFF. Le widget est affiché ci-dessous dans l’état activé/désactivé :

[![Captures d’écran d’un widget de commutateur en désactivant et sur les États](switch-images/16-switch-onoff.png)](switch-images/16-switch-onoff.png#lightbox)

## <a name="creating-a-switch"></a>Création d’un commutateur

Pour créer un commutateur, il vous suffit de déclarer un `Switch` élément dans XML comme suit :

```xml
<Switch android:layout_width="wrap_content"
        android:layout_height="wrap_content" />
```

Cela crée un commutateur de base comme indiqué ci-dessous :

[![Capture d’écran de l’application de démonstration affichant un commutateur en état désactivé](switch-images/07-switch.png)](switch-images/07-switch.png#lightbox)

## <a name="changing-default-values"></a>Modification des valeurs par défaut

Le texte affiché par le contrôle pour les États ACTIVÉs et désactivés et la valeur par défaut est configurable. Par exemple, pour activer le commutateur par défaut et en lecture non/oui au lieu de OFF/ON, nous pouvons définir `checked` les `textOn` attributs, et `textOff` dans le code XML suivant.

```xml
<Switch android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:checked="true"
        android:textOn="YES"
        android:textOff="NO" />
```

## <a name="providing-a-title"></a>Fournir un titre

Le `Switch` widget prend également en charge l’ajout d’une étiquette de texte en définissant l' `text` attribut comme suit :

```xml
<Switch android:text="Is Xamarin.Android great?"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:checked="true"
        android:textOn="YES"
        android:textOff="NO" />
```

Ce balisage produit la capture d’écran suivante au moment de l’exécution :

[![Capture d’écran de l’application de démonstration avec texte horizontalement précédant le widget de commutateur](switch-images/08-switch.png)](switch-images/08-switch.png#lightbox)

Lorsque `Switch` la valeur de est modifiée, elle déclenche un `CheckedChange` événement.
Par exemple, dans le code suivant, nous capturons cet événement et présentons un `Toast` widget avec un message basé sur la `isChecked` valeur de `Switch` , qui est transmise au gestionnaire d’événements dans le cadre de l' `CompoundButton.CheckedChangeEventArg` argument.

```csharp
Switch s = FindViewById<Switch> (Resource.Id.monitored_switch);
           
s.CheckedChange += delegate(object sender, CompoundButton.CheckedChangeEventArgs e) {
    var toast = Toast.MakeText (this, "Your answer is " +
        (e.IsChecked ?  "correct" : "incorrect"), ToastLength.Short);
    toast.Show ();
};
```

## <a name="related-links"></a>Liens associés

- [SwitchDemo (exemple)](/samples/xamarin/monodroid-samples/switchdemo)
- [Didacticiel sur la disposition des onglets](~/android/user-interface/layouts/tab-layout/index.md)