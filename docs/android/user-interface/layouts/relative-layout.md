---
title: Utilisation de RelativeLayout dans Xamarin. Android
description: Utilisation de RelativeLayout dans une application Xamarin. Android
ms.prod: xamarin
ms.assetid: AFD9C849-02C3-E728-BC78-77A563612BC5
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/29/2018
ms.openlocfilehash: af74ae3c7c87f501bff519bcfa361264205ca3f1
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69522382"
---
# <a name="xamarinandroid-relativelayout"></a>Xamarin. Android RelativeLayout

[`RelativeLayout`](xref:Android.Widget.RelativeLayout)est un [`ViewGroup`](xref:Android.Views.ViewGroup) qui affiche l’enfant[`View`](xref:Android.Views.View)
éléments en positions relatives. La position d’un [`View`](xref:Android.Views.View) peut être spécifiée par rapport aux éléments frères (par exemple, à gauche ou en dessous d’un élément donné) ou aux positions relatives au[`RelativeLayout`](xref:Android.Widget.RelativeLayout)
zone (par exemple, alignée en bas à gauche du centre).

Un [`RelativeLayout`](xref:Android.Widget.RelativeLayout) est un utilitaire très puissant pour la conception d’une interface utilisateur, car il peut [`ViewGroup`](xref:Android.Views.ViewGroup)éliminer les s imbriquées. Si vous utilisez plusieurs[`LinearLayout`](xref:Android.Widget.LinearLayout)
les groupes, vous pourrez peut-être les remplacer par un [`RelativeLayout`](xref:Android.Widget.RelativeLayout)seul.

Démarrez un nouveau projet nommé **HelloRelativeLayout**.

Ouvrez le fichier Resources **/Layout/main. AXML** et insérez ce qui suit:

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <TextView
        android:id="@+id/label"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Type here:"/>
    <EditText
        android:id="@+id/entry"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:background="@android:drawable/editbox_background"
        android:layout_below="@id/label"/>
    <Button
        android:id="@+id/ok"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_below="@id/entry"
        android:layout_alignParentRight="true"
        android:layout_marginLeft="10dip"
        android:text="OK" />
    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_toLeftOf="@id/ok"
        android:layout_alignTop="@id/ok"
        android:text="Cancel" />
</RelativeLayout>
```

Notez chacun des `android:layout_*` attributs, tels que `layout_below`, `layout_alignParentRight`et `layout_toLeftOf`.
Lorsque vous utilisez [`RelativeLayout`](xref:Android.Widget.RelativeLayout)un, vous pouvez utiliser ces attributs pour décrire la façon dont vous souhaitez [`View`](xref:Android.Views.View)positionner chacun d’entre eux. Chacun de ces attributs définit un autre type de position relative. Certains attributs utilisent l’ID de ressource d’un [`View`](xref:Android.Views.View) frère pour définir sa propre position relative. Par exemple, la dernière [`Button`](xref:Android.Widget.Button) valeur est définie pour se trouver à gauche et alignée-with-the-Top- [`View`](xref:Android.Views.View) du identifié par l’ID `ok` (qui est le précédent [`Button`](xref:Android.Widget.Button)).

Tous les attributs de disposition disponibles sont définis dans [`RelativeLayout.LayoutParams`](xref:Android.Widget.RelativeLayout.LayoutParams).

Veillez à charger cette disposition dans la[`OnCreate()`](xref:Android.App.Activity.OnCreate*)
méthode

```csharp
protected override void OnCreate (Bundle savedInstanceState)
{
    base.OnCreate (savedInstanceState);
    SetContentView (Resource.Layout.Main);
}
```

La [`SetContentView(int)`](xref:Android.App.Activity.SetContentView*) méthode charge le fichier de disposition pour [`Activity`](xref:Android.App.Activity)le, spécifié par l’ID &mdash; `Resource.Layout.Main` de ressource, fait référence au fichier de disposition Resources **/Layout/main. AXML** .

Exécutez l'application. Vous devez voir la disposition suivante:

[![Capture d’écran d’une disposition relative avec TextView, EditText et deux boutons](relative-layout-images/helloviews2.png)](relative-layout-images/helloviews2.png#lightbox)

## <a name="resources"></a>Ressources

- [`RelativeLayout`](xref:Android.Widget.RelativeLayout)
- [`RelativeLayout.LayoutParams`](xref:Android.Widget.RelativeLayout.LayoutParams)
- [`TextView`](xref:Android.Widget.TextView)
- [`EditText`](xref:Android.Widget.EditText)
- [`Button`](xref:Android.Widget.Button)

_Certaines parties de cette page sont des modifications basées sur le travail créé et partagé par le projet open source Android et utilisées conformément aux termes décrits dans la [licence d’attribution de Creative-2,5](http://creativecommons.org/licenses/by/2.5/)._
