---
title: Menu contextuel
description: Comment ajouter un menu contextuel qui est ancré à une vue particulière.
ms.prod: xamarin
ms.assetid: 1C58E12B-4634-4691-BF59-D5A3F6B0E6F7
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/31/2018
ms.openlocfilehash: e7665ee1d3506fb4b6a237a7c6906d9bfb3e9cb1
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68510211"
---
# <a name="xamarinandroid-popup-menu"></a>Menu contextuel Xamarin. Android

Le [menu](xref:Android.Widget.PopupMenu) contextuel (également appelé _menu contextuel_) est un menu ancré à une vue particulière. Dans l’exemple suivant, une activité unique contient un bouton. Quand l’utilisateur appuie sur le bouton, un menu contextuel à trois éléments s’affiche:

[![Exemple d’application avec un bouton et un menu contextuel à trois éléments](popup-menu-images/01-app-example-sml.png)](popup-menu-images/01-app-example.png#lightbox)


## <a name="creating-a-popup-menu"></a>Création d’un menu contextuel

La première étape consiste à créer un fichier de ressources de menu pour le menu et à le placer dans **ressources/menu**. Par exemple, le code XML suivant est le code du menu à trois éléments affiché dans la capture d’écran précédente, Resources **/menu/popup_menu. xml**:

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:id="@+id/item1"
          android:title="item 1" />
    <item android:id="@+id/item1"
          android:title="item 2" />
    <item android:id="@+id/item1"
          android:title="item 3" />
</menu>
```

Ensuite, créez une instance de `PopupMenu` et Ancrez-la à sa vue. Quand vous créez une instance de `PopupMenu`, vous transmettez `Context` à son constructeur une référence à, ainsi que la vue à laquelle le menu sera attaché. Par conséquent, le menu contextuel est ancré à cette vue pendant sa construction.

Dans l’exemple suivant, le `PopupMenu` est créé dans le gestionnaire d’événements Click pour le bouton (qui est `showPopupMenu`nommé). Ce bouton est également l’affichage auquel le `PopupMenu` est ancré, comme illustré dans l’exemple de code suivant:

```csharp
showPopupMenu.Click += (s, arg) => {
    PopupMenu menu = new PopupMenu (this, showPopupMenu);
};
```

Enfin, le menu contextuel doit être *gonflé* à la ressource de menu créée précédemment. Dans l’exemple suivant, l’appel à la méthode [gonflé](xref:Android.Views.LayoutInflater.Inflate*) du menu est ajouté et sa méthode [Show](xref:Android.Widget.PopupMenu.Show) est appelée pour l’afficher:

```csharp
showPopupMenu.Click += (s, arg) => {
    PopupMenu menu = new PopupMenu (this, showPopupMenu);
    menu.Inflate (Resource.Menu.popup_menu);
    menu.Show ();
};
```


## <a name="handling-menu-events"></a>Gestion des événements de menu

Lorsque l’utilisateur sélectionne un élément de menu, l’événement [MenuItemClick](xref:Android.Widget.PopupMenu.MenuItemClick) Click est déclenché et le menu est fermé. Si vous appuyez n’importe où en dehors du menu, vous le faites simplement disparaître. Dans les deux cas, lorsque le menu est fermé, son [DismissEvent](xref:Android.Widget.PopupMenu.Dismiss) est déclenché. Le code suivant ajoute des gestionnaires d’événements pour les `MenuItemClick` événements `DismissEvent` et:

```csharp
showPopupMenu.Click += (s, arg) => {
    PopupMenu menu = new PopupMenu (this, showPopupMenu);
    menu.Inflate (Resource.Menu.popup_menu);

    menu.MenuItemClick += (s1, arg1) => {
        Console.WriteLine ("{0} selected", arg1.Item.TitleFormatted);
    };

    menu.DismissEvent += (s2, arg2) => {
        Console.WriteLine ("menu dismissed");
    };
    menu.Show ();
};
```



## <a name="related-links"></a>Liens associés

- [PopupMenuDemo (sample)](https://developer.xamarin.com/samples/monodroid/PopupMenuDemo/)
