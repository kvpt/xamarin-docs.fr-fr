---
title: Utilisation de RelativeLayout dans Xamarin. Android
description: Utilisation de RelativeLayout dans une application Xamarin. Android
ms.prod: xamarin
ms.assetid: AFD9C849-02C3-E728-BC78-77A563612BC5
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/29/2018
ms.openlocfilehash: 6cac771f46242cc0475be0a7ec0d475950f4b4e1
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028794"
---
# <a name="xamarinandroid-relativelayout"></a>Xamarin. Android RelativeLayout

[`RelativeLayout`](xref:Android.Widget.RelativeLayout) est un [`ViewGroup`](xref:Android.Views.ViewGroup) qui affiche des [`View`](xref:Android.Views.View) enfants
éléments en positions relatives. La position d’un [`View`](xref:Android.Views.View) peut être spécifiée par rapport à des éléments frères (par exemple, à gauche ou en dessous d’un élément donné) ou à des positions relatives à la [`RelativeLayout`](xref:Android.Widget.RelativeLayout)
zone (par exemple, alignée en bas à gauche du centre).

Un [`RelativeLayout`](xref:Android.Widget.RelativeLayout) est un utilitaire très puissant pour la conception d’une interface utilisateur, car il permet d’éliminer les [`ViewGroup`s ](xref:Android.Views.ViewGroup)imbriqués. Si vous utilisez plusieurs [`LinearLayout`](xref:Android.Widget.LinearLayout) imbriquées
les groupes, vous pourrez peut-être les remplacer par une seule [`RelativeLayout`](xref:Android.Widget.RelativeLayout).

Démarrez un nouveau projet nommé **HelloRelativeLayout**.

Ouvrez le fichier **Resources/layout/main. AXML** et insérez ce qui suit :

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

Notez chaque attribut `android:layout_*`, tel que `layout_below`, `layout_alignParentRight`et `layout_toLeftOf`.
Lorsque vous utilisez un [`RelativeLayout`](xref:Android.Widget.RelativeLayout), vous pouvez utiliser ces attributs pour décrire la façon dont vous souhaitez positionner chaque [`View`](xref:Android.Views.View). Chacun de ces attributs définit un autre type de position relative. Certains attributs utilisent l’ID de ressource d’un [`View`](xref:Android.Views.View) frère pour définir sa propre position relative. Par exemple, la dernière [`Button`](xref:Android.Widget.Button) est définie pour se trouver à gauche et alignée-with-the-top du [`View`](xref:Android.Views.View) identifié par l’ID `ok` (qui est le [`Button`](xref:Android.Widget.Button)précédent).

Tous les attributs de disposition disponibles sont définis dans [`RelativeLayout.LayoutParams`](xref:Android.Widget.RelativeLayout.LayoutParams).

Veillez à charger cette disposition dans le [`OnCreate()`](xref:Android.App.Activity.OnCreate*)
méthode

```csharp
protected override void OnCreate (Bundle savedInstanceState)
{
    base.OnCreate (savedInstanceState);
    SetContentView (Resource.Layout.Main);
}
```

La méthode [`SetContentView(int)`](xref:Android.App.Activity.SetContentView*) charge le fichier de disposition pour l' [`Activity`](xref:Android.App.Activity), spécifié par l’ID de ressource &mdash; `Resource.Layout.Main` fait référence au fichier de disposition **Resources/layout/main. AXML** .

Exécutez l'application. Vous devez voir la disposition suivante :

[Capture d’écran ![d’une disposition relative avec TextView, EditText et deux boutons](relative-layout-images/helloviews2.png)](relative-layout-images/helloviews2.png#lightbox)

## <a name="resources"></a>Ressources

- [`RelativeLayout`](xref:Android.Widget.RelativeLayout)
- [`RelativeLayout.LayoutParams`](xref:Android.Widget.RelativeLayout.LayoutParams)
- [`TextView`](xref:Android.Widget.TextView)
- [`EditText`](xref:Android.Widget.EditText)
- [`Button`](xref:Android.Widget.Button)

_Certaines parties de cette page sont des modifications basées sur le travail créé et partagé par le projet open source Android et utilisées conformément aux termes décrits dans la [licence d’attribution de Creative-2,5](https://creativecommons.org/licenses/by/2.5/)._
