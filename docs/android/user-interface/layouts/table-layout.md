---
title: Xamarin. Android TableLayout
ms.prod: xamarin
ms.assetid: 0C7B9C95-5E5F-A069-BA37-984E49F7DCAD
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/06/2018
ms.openlocfilehash: 4175b1fa62b2bc0e4209d13934c2bdbdd1e2a085
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028747"
---
# <a name="xamarinandroid-tablelayout"></a>Xamarin. Android TableLayout

[`TableLayout`](xref:Android.Widget.TableLayout) est un [`ViewGroup`](xref:Android.Views.ViewGroup)
qui affiche les [`View`](xref:Android.Views.View) enfants
éléments dans les lignes et les colonnes.

Démarrez un nouveau projet nommé **HelloTableLayout**.

Ouvrez le fichier **Resources/layout/main. AXML** et insérez ce qui suit :

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

Notez que cela ressemble à la structure d’une table HTML. [`TableLayout`](xref:Android.Widget.TableLayout)
l’élément est semblable à l’élément de `<table>` HTML ; [`TableRow`](xref:Android.Widget.TableRow)
est semblable à un élément `<tr>` ; Toutefois, pour les cellules, vous pouvez utiliser n’importe quel type d’élément [`View`](xref:Android.Views.View) . Dans cet exemple, un [`TextView`](xref:Android.Widget.TextView)
est utilisé pour chaque cellule. Entre certaines lignes, il y a également un [`View`](xref:Android.Views.View)de base, qui est utilisé pour dessiner une ligne horizontale.

Assurez-vous que votre activité **HelloTableLayout** charge cette disposition dans le [`OnCreate()`](xref:Android.App.Activity.OnCreate*)
méthode

```csharp
protected override void OnCreate (Bundle savedInstanceState)
{
    base.OnCreate (savedInstanceState);
    SetContentView (Resource.Layout.Main);
}
```

La méthode [`SetContentView(int)`](xref:Android.App.Activity.SetContentView*)) charge le fichier de disposition pour l' [`Activity`](xref:Android.App.Activity), spécifié par l’ID de ressource &mdash; `Resource.Layout.Main` fait référence au fichier de disposition **Resources/layout/main. AXML** .

Exécutez l'application. Les éléments suivants doivent s’afficher :

[![exemple de capture d’écran de l’application TableLayout affichant plusieurs lignes de table](table-layout-images/helloviews3.png)](table-layout-images/helloviews3.png#lightbox)

## <a name="references"></a>Références

- [`TableLayout`](xref:Android.Widget.TableLayout)
- [`TableRow`](xref:Android.Widget.TableRow)
- [`TextView`](xref:Android.Widget.TextView)

_Certaines parties de cette page sont des modifications basées sur le travail créé et partagé par le projet open source Android et utilisées conformément aux termes décrits dans la [licence d’attribution de Creative-2,5](https://creativecommons.org/licenses/by/2.5/)._
