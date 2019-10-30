---
title: Xamarin. Android élément LinearLayout
ms.prod: xamarin
ms.assetid: B49D129C-AF24-3C5A-C833-5A34AFBB2442
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/07/2018
ms.openlocfilehash: b1cff01c66ae2581a68286e62bd8c8c5fb7f9d72
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028954"
---
# <a name="xamarinandroid-linearlayout"></a>Xamarin. Android élément LinearLayout

[`LinearLayout`](xref:Android.Widget.LinearLayout) est un [`ViewGroup`](xref:Android.Views.ViewGroup)
qui affiche les [`View`](xref:Android.Views.View) enfants
éléments dans une direction linéaire, verticalement ou horizontalement.

Vous devez faire attention à la surutilisation de la [`LinearLayout`](xref:Android.Widget.LinearLayout).
Si vous commencez à imbriquer plusieurs [`LinearLayout`](xref:Android.Widget.LinearLayout), vous souhaiterez peut-être utiliser une [`RelativeLayout`](xref:Android.Widget.RelativeLayout)
qu'.

Démarrez un nouveau projet nommé **HelloLinearLayout**.

Ouvrez **ressources/mise en page/main. AXML** et insérez ce qui suit :

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation=    "vertical"
    android:layout_width=    "match_parent"
    android:layout_height=    "match_parent"    >

  <LinearLayout
      android:orientation=    "horizontal"
      android:layout_width=    "match_parent"
      android:layout_height=    "match_parent"
      android:layout_weight=    "1"    >
      <TextView
          android:text=    "red"
          android:gravity=    "center_horizontal"
          android:background=    "#aa0000"
          android:layout_width=    "wrap_content"
          android:layout_height=    "match_parent"
          android:layout_weight=    "1"    />
      <TextView
          android:text=    "green"
          android:gravity=    "center_horizontal"
          android:background=    "#00aa00"
          android:layout_width=    "wrap_content"
          android:layout_height=    "match_parent"
          android:layout_weight=    "1"    />
      <TextView
          android:text=    "blue"
          android:gravity=    "center_horizontal"
          android:background=    "#0000aa"
          android:layout_width=    "wrap_content"
          android:layout_height=    "match_parent"
          android:layout_weight=    "1"    />
      <TextView
          android:text=    "yellow"
          android:gravity=    "center_horizontal"
          android:background=    "#aaaa00"
          android:layout_width=    "wrap_content"
          android:layout_height=    "match_parent"
          android:layout_weight=    "1"    />
  </LinearLayout>
        
  <LinearLayout
    android:orientation=    "vertical"
    android:layout_width=    "match_parent"
    android:layout_height=    "match_parent"
    android:layout_weight=    "1"    >
    <TextView
        android:text=    "row one"
        android:textSize=    "15pt"
        android:layout_width=    "match_parent"
        android:layout_height=    "wrap_content"
        android:layout_weight=    "1"    />
    <TextView
        android:text=    "row two"
        android:textSize=    "15pt"
        android:layout_width=    "match_parent"
        android:layout_height=    "wrap_content"
        android:layout_weight=    "1"    />
    <TextView
        android:text=    "row three"
        android:textSize=    "15pt"
        android:layout_width=    "match_parent"
        android:layout_height=    "wrap_content"
        android:layout_weight=    "1"    />
    <TextView
        android:text=    "row four"
        android:textSize=    "15pt"
        android:layout_width=    "match_parent"
        android:layout_height=    "wrap_content"
       android:layout_weight=    "1"    />
  </LinearLayout>

</LinearLayout>
```

Inspectez attentivement ce fichier XML. Il existe un [`LinearLayout`](xref:Android.Widget.LinearLayout) racine
qui définit son orientation verticale &ndash; tous les [`View`](xref:Android.Views.View)enfants (dont il a deux) seront empilés verticalement. Le premier enfant est un autre [`LinearLayout`](xref:Android.Widget.LinearLayout)
qui utilise une orientation horizontale et le deuxième enfant est un [`LinearLayout`](xref:Android.Widget.LinearLayout)
qui utilise une orientation verticale. Chacun de ces [`LinearLayout`](xref:Android.Widget.LinearLayout)imbriqués contient plusieurs [`TextView`](xref:Android.Widget.TextView)
éléments, qui sont orientés les uns avec les autres de la manière définie par leur [`LinearLayout`](xref:Android.Widget.LinearLayout)parent.

Ouvrez maintenant **HelloLinearLayout.cs** et assurez-vous qu’il charge la disposition **Resources/layout/main. axml** dans le [`OnCreate()`](xref:Android.App.Activity.OnCreate*)
méthode

```csharp
protected override void OnCreate (Bundle savedInstanceState)
{
    base.OnCreate (savedInstanceState);
    SetContentView (Resource.Layout.Main);
}
```

La méthode [`SetContentView(int)`](xref:Android.App.Activity.SetContentView*)) charge le fichier de disposition pour l' [`Activity`](xref:Android.App.Activity), spécifié par l’ID de ressource &ndash; `Resources.Layout.Main` fait référence au fichier de disposition **Resources/layout/main. AXML** .

Exécutez l'application. Les éléments suivants doivent s’afficher :

[Capture d’écran ![de l’application First élément LinearLayout organisée horizontalement, en secondes verticalement](linear-layout-images/helloviews1.png)](linear-layout-images/helloviews1.png#lightbox)

Notez comment les attributs XML définissent le comportement de chaque vue. Essayez d’expérimenter différentes valeurs pour `android:layout_weight` pour voir comment le patrimoine de l’écran est distribué en fonction du poids de chaque élément. Pour plus d’informations [sur la façon](https://developer.android.com/guide/topics/ui/declaring-layout.html) dont [`LinearLayout`](xref:Android.Widget.LinearLayout)
gère l’attribut `android:layout_weight`.

## <a name="references"></a>Références

- [`LinearLayout`](xref:Android.Widget.LinearLayout)
- [`TextView`](xref:Android.Widget.TextView)

_Certaines parties de cette page sont des modifications basées sur le travail créé et partagé par le projet open source Android et utilisées conformément aux termes décrits dans la [licence d’attribution de Creative-2,5](https://creativecommons.org/licenses/by/2.5/)._
