---
title: GridLayout
ms.prod: xamarin
ms.assetid: B69A4BF5-9CFB-443A-9F7B-062D1E498F61
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: ef965bed5b50402e1659e45c55a02f9382001521
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68511491"
---
# <a name="xamarinandroid-gridlayout"></a>Xamarin. Android GridLayout

Est `GridLayout` une nouvelle `ViewGroup` sous-classe qui prend en charge la disposition des vues dans une grille 2D, similaire à un tableau HTML, comme indiqué ci-dessous:

 [![GridLayout rogné affichant quatre cellules](grid-layout-images/21-gridlayoutcropped.png)](grid-layout-images/21-gridlayoutcropped.png#lightbox)

 `GridLayout`fonctionne avec une hiérarchie d’affichage plat, où les vues enfants définissent leurs emplacements dans la grille en spécifiant les lignes et les colonnes dans lesquelles elles doivent se trouver. De cette façon, *GridLayout* peut positionner les vues dans la grille sans exiger que les vues intermédiaires fournissent une structure de table, telle qu’elle apparaît dans les lignes de table utilisées dans le TableLayout. En conservant une hiérarchie plate, *GridLayout* peut mettre en forme plus rapidement ses vues enfants. Examinons un exemple pour illustrer ce que signifie réellement ce concept dans le code.


## <a name="creating-a-grid-layout"></a>Création d’une disposition de grille

Le code XML suivant ajoute `TextView` plusieurs contrôles à *GridLayout*.

```xml
<?xml version="1.0" encoding="utf-8"?>
<GridLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"    
        android:rowCount="2"
        android:columnCount="2">
     <TextView
            android:text="Cell 0"
            android:textSize="14dip" />
     <TextView
            android:text="Cell 1"
            android:textSize="14dip" />
     <TextView
            android:text="Cell 2"
            android:textSize="14dip" />
     <TextView
            android:text="Cell 3"
            android:textSize="14dip" />
</GridLayout>
```

La disposition ajuste la taille des lignes et des colonnes afin que les cellules puissent correspondre à leur contenu, comme illustré dans le diagramme suivant:

 [![Diagramme de disposition montrant deux cellules à gauche inférieure à la droite](grid-layout-images/gridlayout-cells.png)](grid-layout-images/gridlayout-cells.png#lightbox)

Cela entraîne l’interface utilisateur suivante lorsqu’elle est exécutée dans une application:

 [![Capture d’écran de l’application GridLayoutDemo affichant quatre cellules](grid-layout-images/01-gridlayout.png)](grid-layout-images/01-gridlayout.png#lightbox)



## <a name="specifying-orientation"></a>Spécification de l’orientation

Notez que dans le code XML ci `TextView` -dessus, chacun ne spécifie pas de ligne ou de colonne. Lorsque ces derniers ne sont pas spécifiés, le `GridLayout` attribue chaque vue enfant dans l’ordre, en fonction de l’orientation. Par exemple, nous allons modifier l’orientation de GridLayout de la valeur par défaut, qui est horizontale, en vertical comme suit:

```xml
<GridLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"    
        android:rowCount="2"
        android:columnCount="2"
        android:orientation="vertical">
</GridLayout>
```

À présent, `GridLayout` le positionne les cellules de haut en bas dans chaque colonne, et non de gauche à droite, comme indiqué ci-dessous:

 [![Diagramme illustrant le positionnement des cellules en orientation verticale](grid-layout-images/gridlayoutorientation.png)](grid-layout-images/gridlayoutorientation.png#lightbox)

L’interface utilisateur suivante est alors générée au moment de l’exécution:

 [![Capture d’écran de GridLayoutDemo avec des cellules positionnées en orientation verticale](grid-layout-images/02-gridlayout.png)](grid-layout-images/02-gridlayout.png#lightbox)



### <a name="specifying-explicit-position"></a>Spécification de la position explicite

Si vous souhaitez contrôler explicitement les positions des vues enfants dans `GridLayout`, nous pouvons définir leurs `layout_row` attributs et `layout_column` . Par exemple, le code XML suivant entraîne l’affichage de la disposition dans la première capture d’écran (illustrée ci-dessus), quelle que soit l’orientation.

```xml
<?xml version="1.0" encoding="utf-8"?>
<GridLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"    
        android:rowCount="2"
        android:columnCount="2">
     <TextView
            android:text="Cell 0"
            android:textSize="14dip"
            android:layout_row="0"
            android:layout_column="0" />
     <TextView
            android:text="Cell 1"
            android:textSize="14dip"
            android:layout_row="0"
            android:layout_column="1" />
     <TextView
            android:text="Cell 2"
            android:textSize="14dip"
            android:layout_row="1"
            android:layout_column="0" />
     <TextView
            android:text="Cell 3"
            android:textSize="14dip"
            android:layout_row="1"
            android:layout_column="1"  />
</GridLayout>
```



### <a name="specifying-spacing"></a>Spécification de l’espacement

Nous disposons de deux options qui fournissent l’espacement entre les vues enfants de `GridLayout`. Nous pouvons utiliser l' `layout_margin` attribut pour définir la marge de chaque vue enfant directement, comme indiqué ci-dessous.

```xml
<TextView
            android:text="Cell 0"
            android:textSize="14dip"
            android:layout_row="0"
            android:layout_column="0"
            android:layout_margin="10dp" />
```

En outre, dans Android 4, une nouvelle vue d’espacement à usage `Space` général appelée est désormais disponible. Pour l’utiliser, il vous suffit de l’ajouter en tant que vue enfant.
Par exemple, le code XML ci-dessous ajoute une ligne `GridLayout` supplémentaire à la `rowcount` méthode en définissant sa valeur `Space` sur 3 et ajoute une vue `TextViews`qui fournit l’espacement entre les.

```xml
<?xml version="1.0" encoding="utf-8"?>
<GridLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"    
        android:rowCount="3"
        android:columnCount="2"
        android:orientation="vertical">
     <TextView
            android:text="Cell 0"
            android:textSize="14dip"
            android:layout_row="0"
            android:layout_column="0" />
     <TextView
            android:text="Cell 1"
            android:textSize="14dip"
            android:layout_row="0"        
            android:layout_column="1" />
     <Space
            android:layout_row="1"
            android:layout_column="0"
            android:layout_width="50dp"         
            android:layout_height="50dp" />    
     <TextView
            android:text="Cell 2"
            android:textSize="14dip"
            android:layout_row="2"        
            android:layout_column="0" />
     <TextView
            android:text="Cell 3"
            android:textSize="14dip"
            android:layout_row="2"        
            android:layout_column="1" />
</GridLayout>
```

Ce code XML crée un espacement dans le `GridLayout` , comme indiqué ci-dessous:

 [![Capture d’écran des GridLayoutDemo illustrant des cellules plus grandes avec espacement](grid-layout-images/03-gridlayout.png)](grid-layout-images/03-gridlayout.png#lightbox)

L’avantage de l’utilisation de `Space` la nouvelle vue est qu’elle autorise l’espacement et ne nécessite pas de définir des attributs sur chaque vue enfant.



### <a name="spanning-columns-and-rows"></a>Fractionnement des colonnes et des lignes

Prend `GridLayout` également en charge les cellules qui s’étendent sur plusieurs colonnes et lignes. Par exemple, imaginons `GridLayout` que nous ajoutions une autre ligne contenant un bouton au, comme indiqué ci-dessous:

```xml
<?xml version="1.0" encoding="utf-8"?>
<GridLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"    
        android:rowCount="4"
        android:columnCount="2"
        android:orientation="vertical">
     <TextView
            android:text="Cell 0"
            android:textSize="14dip"
            android:layout_row="0"
            android:layout_column="0" />
     <TextView
            android:text="Cell 1"
            android:textSize="14dip"
            android:layout_row="0"        
            android:layout_column="1" />
     <Space
            android:layout_row="1"
            android:layout_column="0"
            android:layout_width="50dp"        
            android:layout_height="50dp" />   
     <TextView
            android:text="Cell 2"
            android:textSize="14dip"
            android:layout_row="2"        
            android:layout_column="0" />
     <TextView
            android:text="Cell 3"
            android:textSize="14dip"        
            android:layout_row="2"        
            android:layout_column="1" />
     <Button
            android:id="@+id/myButton"
            android:text="@string/hello"        
            android:layout_row="3"
            android:layout_column="0" />
</GridLayout>
```

La première colonne du `GridLayout` est alors étirée pour s’adapter à la taille du bouton, comme nous le voyons ici:

[![Capture d’écran de GridLayoutDemo avec un bouton qui fractionne uniquement la première colonne](grid-layout-images/04-gridlayout.png)](grid-layout-images/04-gridlayout.png#lightbox)

Pour éviter l’étirement de la première colonne, nous pouvons définir le bouton pour qu’il s’étende sur deux colonnes en définissant son ColumnSpan comme suit:

```xml
<Button
    android:id="@+id/myButton"
    android:text="@string/hello"       
    android:layout_row="3"
    android:layout_column="0"
    android:layout_columnSpan="2" />
```

Cela aboutit à une mise en page `TextViews` du qui est similaire à la disposition que nous avions précédemment, avec le bouton ajouté au bas de `GridLayout` la, comme indiqué ci-dessous:

 [![Capture d’écran de GridLayoutDemo avec un bouton qui couvre les deux colonnes](grid-layout-images/05-gridlayout.png)](grid-layout-images/05-gridlayout.png#lightbox)


## <a name="related-links"></a>Liens associés

- [GridLayoutDemo (exemple)](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/ICS_Samples/GridLayoutDemo/)
- [Présentation du sandwich glacé](http://www.android.com/about/ice-cream-sandwich/)
- [Plateforme Android 4,0](https://developer.android.com/sdk/android-4.0.html)
