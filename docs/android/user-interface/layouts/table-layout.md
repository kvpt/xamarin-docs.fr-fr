---
title: Xamarin. Android TableLayout
ms.prod: xamarin
ms.assetid: 0C7B9C95-5E5F-A069-BA37-984E49F7DCAD
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 1c477f030dc69394ba601b31d71a772f5037af48
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69522297"
---
# <a name="xamarinandroid-tablelayout"></a>Xamarin. Android TableLayout

[`TableLayout`](xref:Android.Widget.TableLayout)est un[`ViewGroup`](xref:Android.Views.ViewGroup)
qui affiche l’enfant[`View`](xref:Android.Views.View)
éléments dans les lignes et les colonnes.

Démarrez un nouveau projet nommé **HelloTableLayout**.

Ouvrez le fichier Resources **/Layout/main. AXML** et insérez ce qui suit:

```xml
<?xml version="1.0" encoding="utf-8"?>
<TableLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:stretchColumns="1">

    <TableRow>
        <TextView
            android:layout_column="1"
            android:text="Open..."
            android:padding="3dip"/>
        <TextView
            android:text="Ctrl-O"
            android:gravity="right"
            android:padding="3dip"/>
    </TableRow>

    <TableRow>
        <TextView
            android:layout_column="1"
            android:text="Save..."
            android:padding="3dip"/>
        <TextView
            android:text="Ctrl-S"
            android:gravity="right"
            android:padding="3dip"/>
    </TableRow>

    <TableRow>
        <TextView
            android:layout_column="1"
            android:text="Save As..."
            android:padding="3dip"/>
        <TextView
            android:text="Ctrl-Shift-S"
            android:gravity="right"
            android:padding="3dip"/>
    </TableRow>

    <View
        android:layout_height="2dip"
        android:background="#FF909090"/>

    <TableRow>
        <TextView
            android:text="X"
            android:padding="3dip"/>
        <TextView
            android:text="Import..."
            android:padding="3dip"/>
    </TableRow>

    <TableRow>
        <TextView
            android:text="X"
            android:padding="3dip"/>
        <TextView
            android:text="Export..."
            android:padding="3dip"/>
        <TextView
            android:text="Ctrl-E"
            android:gravity="right"
            android:padding="3dip"/>
    </TableRow>

    <View
        android:layout_height="2dip"
        android:background="#FF909090"/>

    <TableRow>
        <TextView
            android:layout_column="1"
            android:text="Quit"
            android:padding="3dip"/>
    </TableRow>
</TableLayout>
```

Notez que cela ressemble à la structure d’une table HTML. La[`TableLayout`](xref:Android.Widget.TableLayout)
l’élément est semblable à `<table>` l’élément HTML;[`TableRow`](xref:Android.Widget.TableRow)
est semblable à `<tr>` un élément, mais pour les cellules, vous pouvez utiliser n’importe [`View`](xref:Android.Views.View) quel type d’élément. Dans cet exemple, un[`TextView`](xref:Android.Widget.TextView)
est utilisé pour chaque cellule. Entre certaines lignes, il y a également un de base [`View`](xref:Android.Views.View), qui est utilisé pour dessiner une ligne horizontale.

Assurez-vous que votre activité **HelloTableLayout** charge cette disposition dans la[`OnCreate()`](xref:Android.App.Activity.OnCreate*)
méthode

```csharp
protected override void OnCreate (Bundle savedInstanceState)
{
    base.OnCreate (savedInstanceState);
    SetContentView (Resource.Layout.Main);
}
```

La [`SetContentView(int)`](xref:Android.App.Activity.SetContentView*)méthode) charge le fichier de disposition pour [`Activity`](xref:Android.App.Activity)le, spécifié par l’ID &mdash; `Resource.Layout.Main` de ressource, fait référence au fichier de disposition Resources **/Layout/main. AXML** .

Exécutez l'application. Les éléments suivants doivent s’afficher:

[![Exemple de capture d’écran de l’application TableLayout affichant plusieurs lignes de table](table-layout-images/helloviews3.png)](table-layout-images/helloviews3.png#lightbox)



## <a name="references"></a>Références

- [`TableLayout`](xref:Android.Widget.TableLayout)
- [`TableRow`](xref:Android.Widget.TableRow)
- [`TextView`](xref:Android.Widget.TextView)

_Certaines parties de cette page sont des modifications basées sur le travail créé et partagé par le projet open source Android et utilisées conformément aux termes décrits dans la [licence d’attribution de Creative-2,5](http://creativecommons.org/licenses/by/2.5/)._
