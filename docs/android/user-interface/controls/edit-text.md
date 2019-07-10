---
title: Modifier le texte
description: Comment utiliser le widget EditText d’accepter l’entrée d’utilisateur.
ms.prod: xamarin
ms.assetid: E513BCBC-438E-15E8-B83A-4B768A8E8B32
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/09/2018
ms.openlocfilehash: 518c13aea431a8e973579768cc70b8281a31acac
ms.sourcegitcommit: 58d8bbc19ead3eb535fb8248710d93ba0892e05d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67674727"
---
# <a name="edit-text"></a>Modifier le texte

Dans cette section, vous allez utiliser le [EditText](https://developer.xamarin.com/api/type/Android.Widget.EditText/) widget pour créer un champ de texte pour l’entrée utilisateur. Une fois que le texte a été entré dans le champ, le **entrée** clé affichera le texte dans un message toast.

Ouvrez **Resources/layout/activity_main.axml** et ajoutez le [EditText](https://developer.xamarin.com/api/type/Android.Widget.EditText/) élément à une disposition contenante. L’exemple suivant **activity_main.axml** a un `EditText` qui a été ajouté à un `LinearLayout`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <EditText
        android:id="@+id/edittext"
        android:layout_width="match_parent"
        android:imeOptions="actionGo"
        android:inputType="text"
        android:layout_height="wrap_content" />
</LinearLayout>
```

Dans cet exemple de code, le `EditText` attribut `android:imeOptions` est défini sur `actionGo`. Ce paramètre modifie la valeur par défaut [fait](https://developer.android.com/reference/android/view/inputmethod/EditorInfo#IME_ACTION_DONE) action à la [accédez](https://developer.android.com/reference/android/view/inputmethod/EditorInfo#IME_ACTION_GO) action afin qu’en appuyant sur la **entrée** clé déclencheurs le `KeyPress` Gestionnaire d’entrée.
(En règle générale, `actionGo` est utilisé afin que le **entrée** clé dirige l’utilisateur vers la cible d’une URL qui est tapée dans.)

Pour gérer l’entrée de texte utilisateur, ajoutez le code suivant à la fin de la [OnCreate](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/) méthode dans **MainActivity.cs**:

```csharp
EditText edittext = FindViewById<EditText>(Resource.Id.edittext);
edittext.KeyPress += (object sender, View.KeyEventArgs e) => {
    e.Handled = false;
    if (e.Event.Action == KeyEventActions.Down && e.KeyCode == Keycode.Enter) 
    {
        Toast.MakeText(this, edittext.Text, ToastLength.Short).Show();
        e.Handled = true;
    }
};
```

En outre, ajoutez le code suivant `using` instruction au début du **MainActivity.cs** si elle n’est pas déjà présent :

```csharp
using Android.Views;
```

Cet exemple de code augmente le [EditText](https://developer.xamarin.com/api/type/Android.Widget.EditText/) élément à partir de la mise en page et ajoute un [KeyPress](https://developer.xamarin.com/api/event/Android.Views.View.KeyPress/) gestionnaire qui définit l’action à effectuer lorsqu’une touche est enfoncée alors que le widget a le focus. Dans ce cas, la méthode est définie pour écouter le **entrée** (lors de l’appui sur) la clé et afficher un [Toast](https://developer.xamarin.com/api/type/Android.Widget.Toast/) message avec le texte qui a été entré. Notez que le [Handled](https://developer.xamarin.com/api/property/Android.Views.View+KeyEventArgs.Handled/) propriété doit toujours être `true` si l’événement a été géré. Cela est nécessaire pour empêcher l’événement de propagation haut (ce qui entraînerait un retour chariot dans le champ de texte).

Exécutez l’application et entrez du texte dans le champ de texte. Quand vous appuyez sur la **entrée** clé, le toast s’affiche comme indiqué sur la droite :

[![Exemples de saisie de texte dans EditText](edit-text-images/edit-text-sml.png)](edit-text-images/edit-text.png#lightbox)

*Certaines parties de cette page sont des modifications basées sur le travail créé et* [ *partagé par Android Open Source Project* ](http://code.google.com/policies.html) *et utilisé conformément aux conditions décrites dans le* [ *Licence creative Commons 2.5 Attribution* ](http://creativecommons.org/licenses/by/2.5/) *. Ce didacticiel est basé sur le* [ *didacticiel d’Android formulaire Stuff* ](https://developer.android.com/resources/tutorials/views/hello-formstuff.html) *.*


## <a name="related-links"></a>Liens associés

- [EditTextSample](https://developer.xamarin.com/samples/monodroid/UserInterface/EditTextSample/)
