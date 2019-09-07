---
title: Xamarin. Android élément LinearLayout
ms.prod: xamarin
ms.assetid: B49D129C-AF24-3C5A-C833-5A34AFBB2442
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/07/2018
ms.openlocfilehash: 14e9b352a309de94a374b52141e3fd61715d8f75
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70764380"
---
# <a name="xamarinandroid-linearlayout"></a>Xamarin. Android élément LinearLayout

[`LinearLayout`](xref:Android.Widget.LinearLayout)est un[`ViewGroup`](xref:Android.Views.ViewGroup)
qui affiche l’enfant[`View`](xref:Android.Views.View)
éléments dans une direction linéaire, verticalement ou horizontalement.

Vous devez être attentif à la [`LinearLayout`](xref:Android.Widget.LinearLayout)surutilisation de.
Si vous commencez à imbriquer plusieurs [`LinearLayout`](xref:Android.Widget.LinearLayout), vous souhaiterez peut-être utiliser un[`RelativeLayout`](xref:Android.Widget.RelativeLayout)
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

Inspectez attentivement ce fichier XML. Il existe une racine[`LinearLayout`](xref:Android.Widget.LinearLayout)
qui définit son orientation verticale &ndash; pour tous les enfants [`View`](xref:Android.Views.View)(dont il en a deux) sera empilé verticalement. Le premier enfant est un autre[`LinearLayout`](xref:Android.Widget.LinearLayout)
qui utilise une orientation horizontale et le deuxième enfant est un[`LinearLayout`](xref:Android.Widget.LinearLayout)
qui utilise une orientation verticale. Chacun de ces [`LinearLayout`](xref:Android.Widget.LinearLayout)s imbriqués contient plusieurs[`TextView`](xref:Android.Widget.TextView)
éléments, qui sont orientés les uns avec les autres de la manière définie [`LinearLayout`](xref:Android.Widget.LinearLayout)par leur parent.

Ouvrez maintenant **HelloLinearLayout.cs** et assurez-vous qu’il charge la disposition **Resources/layout/main. AXML** dans le[`OnCreate()`](xref:Android.App.Activity.OnCreate*)
méthode

```csharp
protected override void OnCreate (Bundle savedInstanceState)
{
    base.OnCreate (savedInstanceState);
    SetContentView (Resource.Layout.Main);
}
```

La [`SetContentView(int)`](xref:Android.App.Activity.SetContentView*)méthode) charge le fichier de disposition pour [`Activity`](xref:Android.App.Activity)le, spécifié par l’ID &ndash; `Resources.Layout.Main` de ressource, fait référence au fichier de disposition **Resources/layout/main. AXML** .

Exécutez l'application. Les éléments suivants doivent s’afficher :

[![Capture d’écran de l’application First élément LinearLayout organisée horizontalement, en secondes verticalement](linear-layout-images/helloviews1.png)](linear-layout-images/helloviews1.png#lightbox)

Notez comment les attributs XML définissent le comportement de chaque vue. Essayez d’expérimenter avec différentes valeurs pour `android:layout_weight` voir comment la surface de l’écran est distribuée en fonction de la pondération de chaque élément. Pour plus d’informations sur la façon de procéder, consultez le document [objets de mise en page courants](https://developer.android.com/guide/topics/ui/declaring-layout.html)[`LinearLayout`](xref:Android.Widget.LinearLayout)
gère l' `android:layout_weight` attribut.

## <a name="references"></a>Références

- [`LinearLayout`](xref:Android.Widget.LinearLayout)
- [`TextView`](xref:Android.Widget.TextView)

_Certaines parties de cette page sont des modifications basées sur le travail créé et partagé par le projet open source Android et utilisées conformément aux termes décrits dans la [licence d’attribution de Creative-2,5](http://creativecommons.org/licenses/by/2.5/)._
