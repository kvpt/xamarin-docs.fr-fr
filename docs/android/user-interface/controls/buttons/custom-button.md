---
title: Bouton personnalisé
ms.prod: xamarin
ms.assetid: C523D41E-5855-248D-079D-6B12B74B7617
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: ecb745f2f50b5aa0e22e331a4def0be9d8f86aa5
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68510398"
---
# <a name="custom-button"></a>Bouton personnalisé

Dans cette section, vous allez créer un bouton avec une image personnalisée au lieu d’un texte, [`Button`](xref:Android.Widget.Button) à l’aide du widget et d’un fichier XML qui définit trois images différentes à utiliser pour les différents États de bouton. Lorsque vous appuyez sur le bouton, un message succinct s’affiche.

Cliquez avec le bouton droit et téléchargez les trois images ci-dessous, puis copiez-les dans le répertoire Resources **/Drawing** de votre projet. Ils seront utilisés pour les différents États de bouton.

 [ ![](custom-button-images/android-focused.png)](custom-button-images/android-focused.png#lightbox) [ ![](custom-button-images/android-pressed.png)](custom-button-images/android-pressed.png#lightbox) [ IcôneAndroidvertepourétatnormalicône![](custom-button-images/android-normal.png)](custom-button-images/android-normal.png#lightbox) Android orange pour l’État ciblé icône Android jaune pour l’état appuyé

Créez un fichier dans le répertoire **Resources/Drawable** nommé **android_button. xml**. Insérez le code XML suivant:

```xml
<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:drawable="@drawable/android_pressed"
          android:state_pressed="true" />
    <item android:drawable="@drawable/android_focused"
          android:state_focused="true" />
    <item android:drawable="@drawable/android_normal" />
</selector>
```

Cela définit une seule ressource pouvant être dessinée, qui modifie son image en fonction de l’état actuel du bouton. La première `<item>` définit **android_pressed. png** comme image lorsque le bouton est enfoncé (elle a été activée); la seconde `<item>` définit **android_focused. png** comme image lorsque le bouton est activé (lorsque le bouton est mise en surbrillance à l’aide du trackball ou du pavé directionnel); et le troisième `<item>` définit **android_normal. png** comme image pour l’état normal (lorsque vous n’appuyez pas sur le bouton ou n’est pas activé). Ce fichier XML représente maintenant une seule ressource dessinable et, lorsqu’il est référencé [`Button`](xref:Android.Widget.Button) par un pour son arrière-plan, l’image affichée change en fonction de ces trois États.


> [!NOTE]
> L’ordre des `<item>` éléments est important. Quand ce dessinable est référencé, les `<item>`s sont parcourues dans l’ordre pour déterminer celui qui convient à l’état du bouton actuel.
> Étant donné que l’image «normale» est la dernière, elle est appliquée uniquement `android:state_pressed` lorsque `android:state_focused` les conditions et ont toutes deux évaluées la valeur false.

Ouvrez le fichier Resources **/Layout/main. AXML** et [`Button`](xref:Android.Widget.Button) ajoutez l’élément:

```xml
<Button
        android:id="@+id/button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:padding="10dp"
        android:background="@drawable/android_button" />
```

L' `android:background` attribut spécifie la ressource pouvant être dessinée à utiliser pour l’arrière-plan du bouton (qui, lorsqu’il est enregistré dans **ressources/dessinable/Android. xml**, est référencée sous la forme `@drawable/android`). Cela remplace l’image d’arrière-plan normale utilisée pour les boutons dans le système. Pour permettre au dessinable de modifier son image en fonction de l’état du bouton, l’image doit être appliquée à l’arrière-plan.

Pour faire en sorte que le bouton effectue une opération quand elle est enfoncée, ajoutez le code suivant à la fin de la[`OnCreate()`](xref:Android.App.Activity.OnCreate*)
méthode

```csharp
Button button = FindViewById<Button>(Resource.Id.button);

button.Click += (o, e) => {
    Toast.MakeText (this, "Beep Boop", ToastLength.Short).Show ();
};
```

Cela permet de capturer [`Button`](xref:Android.Widget.Button) le à partir de la disposition, [`Toast`](xref:Android.Widget.Toast) puis d’ajouter un message à [`Button`](xref:Android.Widget.Button) afficher lorsque l’utilisateur clique sur.

Exécutez maintenant l’application.


*Certaines parties de cette page sont des modifications basées sur le travail créé et partagé par le projet open source Android et utilisées conformément aux termes décrits dans la*
[*licence d’attribution de Creative-2,5*](http://creativecommons.org/licenses/by/2.5/).
