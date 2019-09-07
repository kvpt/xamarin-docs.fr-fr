---
title: Modifier le texte
description: Comment utiliser le widget EditText pour accepter les entrées d’utilisateur.
ms.prod: xamarin
ms.assetid: E513BCBC-438E-15E8-B83A-4B768A8E8B32
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/09/2018
ms.openlocfilehash: e8ffe337e1f5c74bc348b9600a466f1232f40b0b
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70758832"
---
# <a name="xamarinandroid-edit-text"></a>Xamarin. Android-modifier le texte

Dans cette section, vous allez utiliser le widget [EDITTEXT](xref:Android.Widget.EditText) pour créer un champ de texte pour l’entrée utilisateur. Une fois que le texte a été entré dans le champ, la touche **entrée** affiche le texte dans un message Toast.

Ouvrez **Resources/layout/activity_main. AXML** et ajoutez l’élément [EDITTEXT](xref:Android.Widget.EditText) à une disposition contenante. L’exemple suivant **activity_main. AXML** a un `EditText` qui a été ajouté à un `LinearLayout`:

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

Dans cet exemple de code, `EditText` l' `android:imeOptions` attribut a la `actionGo`valeur. Ce paramètre remplace l’action [effectuée](https://developer.android.com/reference/android/view/inputmethod/EditorInfo#IME_ACTION_DONE) par défaut par l’action [atteindre](https://developer.android.com/reference/android/view/inputmethod/EditorInfo#IME_ACTION_GO) afin que le fait d’appuyer sur la `KeyPress` touche **entrée** déclenche le gestionnaire d’entrée.
(En général `actionGo` , est utilisé afin que la touche **entrée** dirige l’utilisateur vers la cible d’une URL qui est tapée dans.)

Pour gérer l’entrée de texte de l’utilisateur, ajoutez le code suivant à la fin de la méthode [OnCreate](xref:Android.App.Activity.OnCreate*) dans **MainActivity.cs**:

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

En outre, ajoutez l’instruction `using` suivante en haut de **MainActivity.cs** si elle n’est pas déjà présente :

```csharp
using Android.Views;
```

Cet exemple de code montre l’élément [EDITTEXT](xref:Android.Widget.EditText) de la disposition et [ajoute un gestionnaire d’activation de clé](xref:Android.Views.View.KeyPress) qui définit l’action à effectuer lorsqu’une touche est enfoncée alors que le widget a le focus. Dans ce cas, la méthode est définie pour écouter la touche **entrée** (lorsqu’elle est exploitée), puis afficher un message [Toast](xref:Android.Widget.Toast) avec le texte entré. Notez que la propriété [gérée](xref:Android.Views.View.KeyEventArgs.Handled) doit toujours être `true` si l’événement a été géré. Cela est nécessaire pour empêcher la propagation de l’événement (ce qui entraînerait un retour chariot dans le champ de texte).

Exécutez l’application et entrez du texte dans le champ de texte. Lorsque vous appuyez sur la touche **entrée** , le Toast s’affiche comme indiqué sur la droite :

[![Exemples d’entrée de texte dans EditText](edit-text-images/edit-text-sml.png)](edit-text-images/edit-text.png#lightbox)

*Certaines parties de cette page sont des modifications basées sur le travail créé et* [*partagé par le projet open source Android*](http://code.google.com/policies.html) *et utilisés conformément aux termes décrits dans le* [*Licence d’attribution Creative-2,5*](http://creativecommons.org/licenses/by/2.5/) *. Ce didacticiel est basé sur le* [*didacticiel de formulaire Android*](https://developer.android.com/resources/tutorials/views/hello-formstuff.html) *.*

## <a name="related-links"></a>Liens associés

- [EditTextSample](https://docs.microsoft.com/samples/xamarin/monodroid-samples/userinterface-edittextsample)
